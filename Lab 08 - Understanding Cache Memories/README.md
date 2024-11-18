``` C
/* 
 * csim.c - A cache simulator that can replay traces from Valgrind
 *     and output statistics such as number of hits, misses, and
 *     evictions.  The replacement policy is LRU.
 *
 * Implementation and assumptions:
 *  1. Each load/store can cause at most one cache miss. (I examined the trace,
 *  the largest request I saw was for 8 bytes).
 *  2. Instruction loads (I) are ignored, since we are interested in evaluating
 *  trans.c in terms of its data cache performance.
 *  3. data modify (M) is treated as a load followed by a store to the same
 *  address. Hence, an M operation can result in two cache hits, or a miss and a
 *  hit plus an possible eviction.
 *
 * The function printSummary() is given to print output.
 * Please use this function to print the number of hits, misses and evictions.
 * This is crucial for the driver to evaluate your work. 
 */
#include <getopt.h>
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <assert.h>
#include <math.h>
#include <limits.h>
#include <string.h>
#include <errno.h>
#include "cachelab.h"

//#define DEBUG_ON 
#define ADDRESS_LENGTH 64

/* Type: Memory address */
typedef unsigned long long int mem_addr_t;

/* Type: Cache line
   LRU is a counter used to implement LRU replacement policy  */
typedef struct cache_line {
    char valid;
    mem_addr_t tag;
    unsigned long long int lru;
} cache_line_t;

typedef cache_line_t* cache_set_t;
typedef cache_set_t* cache_t;

/* Globals set by command line args */
int verbosity = 0; /* print trace if set */
int s = 0; /* set index bits */
int b = 0; /* block offset bits */
int E = 0; /* associativity */
char* trace_file = NULL;

/* Derived from command line args */
int S; /* number of sets */
int B; /* block size (bytes) */

/* Counters used to record cache statistics */
int miss_count = 0;
int hit_count = 0;
int eviction_count = 0;
unsigned long long int lru_counter = 1;

/* The cache we are simulating */
cache_t cache;  
mem_addr_t set_index_mask;

/* 
 * initCache - Allocate memory, write 0's for valid and tag and LRU
 * also computes the set_index_mask
 */
void initCache()
{
    int i,j;
    cache = (cache_set_t*) malloc(sizeof(cache_set_t) * S);
    for (i=0; i<S; i++){
        cache[i]=(cache_line_t*) malloc(sizeof(cache_line_t) * E);
        for (j=0; j<E; j++){
            cache[i][j].valid = 0;
            cache[i][j].tag = 0;
            cache[i][j].lru = 0;
        }
    }

    /* Computes set index mask */
    set_index_mask = (mem_addr_t) (pow(2, s) - 1);
}


/* 
 * freeCache - free allocated memory
 */
void freeCache()
{
    int i;
    for (i=0; i<S; i++){
        free(cache[i]);
    }
    free(cache);
}


/* 
 * accessData - Access data at memory address addr.
 *   If it is already in cache, increast hit_count
 *   If it is not in cache, bring it in cache, increase miss count.
 *   Also increase eviction_count if a line is evicted.
 */
void accessData(mem_addr_t addr)
{
    int i;
    unsigned long long int eviction_lru = ULONG_MAX;
    unsigned int eviction_line = 0;
    mem_addr_t set_index = (addr >> b) & set_index_mask;
    mem_addr_t tag = addr >> (s+b);

    cache_set_t cache_set = cache[set_index];

    for(i=0; i<E; i++){
        if(cache_set[i].tag==tag && cache_set[i].valid){
            hit_count++;
            if(verbosity)
                printf("hit ");
            cache_set[i].lru = lru_counter++;
            return;
        }
    }

    /* If we reach this line, then we have a cache miss */
    miss_count++;
    if (verbosity)
        printf("miss ");
    for(i=0; i<E; i++){
        if (cache_set[i].lru < eviction_lru){
            eviction_line = i;
            eviction_lru = cache_set[i].lru;
        }
    }

    if( cache_set[eviction_line].valid ){
        eviction_count++;
        if (verbosity)
            printf("eviction ");
    }
  
    cache_set[eviction_line].valid = 1;
    cache_set[eviction_line].tag = tag;
    cache_set[eviction_line].lru = lru_counter++;
}


/*
 * replayTrace - replays the given trace file against the cache 
 */
void replayTrace(char* trace_fn)
{
    char buf[1000];
    mem_addr_t addr=0;
    unsigned int len=0;
    FILE* trace_fp = fopen(trace_fn, "r");

    if(!trace_fp){
        fprintf(stderr, "%s: %s\n", trace_fn, strerror(errno));
        exit(1);
    }

    while( fgets(buf, 1000, trace_fp) != NULL) {
        if(buf[1]=='S' || buf[1]=='L' || buf[1]=='M') {
            sscanf(buf+3, "%llx,%u", &addr, &len);
      
            if(verbosity)
                printf("%c %llx,%u ", buf[1], addr, len);

            accessData(addr);

            /* If the instruction is R/W then access again */
            if(buf[1]=='M')
                accessData(addr);
            
            if (verbosity)
                printf("\n");
        }
    }

    fclose(trace_fp);
}

/*
 * printUsage - Print usage info
 */
void printUsage(char* argv[])
{
    printf("Usage: %s [-hv] -s <num> -E <num> -b <num> -t <file>\n", argv[0]);
    printf("Options:\n");
    printf("  -h         Print this help message.\n");
    printf("  -v         Optional verbose flag.\n");
    printf("  -s <num>   Number of set index bits.\n");
    printf("  -E <num>   Number of lines per set.\n");
    printf("  -b <num>   Number of block offset bits.\n");
    printf("  -t <file>  Trace file.\n");
    printf("\nExamples:\n");
    printf("  linux>  %s -s 4 -E 1 -b 4 -t traces/yi.trace\n", argv[0]);
    printf("  linux>  %s -v -s 8 -E 2 -b 4 -t traces/yi.trace\n", argv[0]);
    exit(0);
}

/*
 * main - Main routine 
 */
int main(int argc, char* argv[])
{
    char c;

    while( (c=getopt(argc,argv,"s:E:b:t:vh")) != -1){
        switch(c){
        case 's':
            s = atoi(optarg);
            break;
        case 'E':
            E = atoi(optarg);
            break;
        case 'b':
            b = atoi(optarg);
            break;
        case 't':
            trace_file = optarg;
            break;
        case 'v':
            verbosity = 1;
            break;
        case 'h':
            printUsage(argv);
            exit(0);
        default:
            printUsage(argv);
            exit(1);
        }
    }

    /* Make sure that all required command line args were specified */
    if (s == 0 || E == 0 || b == 0 || trace_file == NULL) {
        printf("%s: Missing required command line argument\n", argv[0]);
        printUsage(argv);
        exit(1);
    }

    /* Compute S, E and B from command line args */
    S = (unsigned int) pow(2, s);
    B = (unsigned int) pow(2, b);
 
    /* Initialize cache */
    initCache();

#ifdef DEBUG_ON
    printf("DEBUG: S:%u E:%u B:%u trace:%s\n", S, E, B, trace_file);
    printf("DEBUG: set_index_mask: %llu\n", set_index_mask);
#endif
 
    replayTrace(trace_file);

    /* Free allocated memory */
    freeCache();

    /* Output the hit and miss statistics for the autograder */
    printSummary(hit_count, miss_count, eviction_count);
    return 0;
}
```


``` C
/* 
 * trans.c - Matrix transpose B = A^T
 *
 * Each transpose function has a prototype of the form:
 *
 * void trans(int M, int N, int A[N][M], int B[M][N]);
 *
 * Each function is evaluated by counting the number of misses 
 * on a 1KB direct mapped cache with a block size of 32 bytes.
 */ 
#include <stdio.h>
#include "cachelab.h"

/* 
 * transpose_submit - This is the solution transpose function that you
 *     will be graded on for Part B of the assignment. Do not change
 *     the description string "Transpose submission", as the driver
 *     searches for that string to identify the transpose function to
 *     be graded. The REQUIRES and ENSURES from 15-122 are included
 *     for your convenience. They can be removed if you like.
 */
char transpose_submit_desc[] = "Transpose submission";
void transpose_submit(int M, int N, int A[N][M], int B[M][N]){
    // Transpose function written by Pranav Senthilnathan
    // Code for 61 x 67 array case from Shrikant Mether
    if(N == 32 && M == 32)
        {
            // Bocking with block size 8x8
            int x,y,i,j;
            for(x=0; x<N; x+=8)
                {
                    for(y=0; y<M; y+=8)
                        {
                            for(i=x; (i<x+8) && (i<N) ; i++)
                                {
                                    for(j=y; (j<y+8) && (j<M); j++ )
                                        {
                                            /* Diagonal entries in diagonal blocks force cache miss
                                             *  In this case the cache has B_i,..,B_{i-1},A_i,B_{i+1},..,B_{i+7}
                                             *  so writing into B_i will evict A_i. But we need to read A_i again
                                             *  so we get another miss while moving A_i back in
                                             *  To alleviate this problem we set B_i at the end, so (1) we
                                             *  won't need to read A_i again and (2) we have B_i when i <- i+1
                                             */
                                            if(y == x && i == j) continue;
                                            B[j][i] = A[i][j];
                                        }
                                    if(y == x) B[i][i] = A[i][i];
                                }
                        }
                }
        }
    else if(N == 64 && M == 64)
        {
            // Blocking with block size 8x8 and subblocks 4x4
            // Overview of an 8x8 block and its subblock:
            // A = ---------  B = ---------
            //     | W | X |      | W'| Y'|
            //     ---------      ---------
            //     | Y | Z |      | X'| Z'|
            //     ---------      ---------
            // 1) Move X^t into X', W^t into Z'
            // 2) Move Z into Z' while moving W^t from Z' to W' 
            // 3) Move XY into Y'
            int x,y,i,j;
            // Temporary storage for step 2. 
            int tmp1, tmp2, tmp3, tmp4, tmp5, tmp6;
            for(x=0; x<N; x+=8){
                for(y=0; y<M; y+=8){
                    // Step 1
                    for(i=x; (i<x+4) && (i<N) ; i++)
                        {
                            for(j=y; (j<y+4) && (j<M); j++ )
                                {
                                    // Save diagonal transpose for later
                                    if(i == j)
                                        {
                                            tmp1 = A[i][i];
                                            tmp2 = A[i][i+4];
                                            continue;
                                        }
                                    B[j+4][i+4] = A[i][j];
                                    B[j+4][i] = A[i][j+4];
                                }
                            if(x == y)
                                {
                                    B[i+4][i+4] = tmp1;
                                    B[i+4][i] = tmp2;
                                }
                        }
    
                    // Step 2           
                    for(j=y+4; (j<y+8) && (j<M) ; j++)
                        {
                            // this A line will be evicted from cache so store it in tmp vars
                            tmp1 = A[x+4][j];
                            tmp2 = A[x+5][j];
                            tmp3 = A[x+6][j];
                            tmp4 = A[x+7][j];

                            // Diagonal entry memoization for step 3
                            if(x == y) tmp5 = A[j][j-4];

                            // Loading B line evicts A, so get A's vals from tmp vars
                            // Also store in B's old vals to move up in the tmp vars
                            tmp6 = B[j][x+4];
                            B[j][x+4] = tmp1;
                            tmp1 = tmp6;

                            tmp6 = B[j][x+5];
                            B[j][x+5] = tmp2;
                            tmp2 = tmp6;

                            tmp6 = B[j][x+6];
                            B[j][x+6] = tmp3;
                            tmp3 = tmp6;

                            tmp6 = B[j][x+7];
                            B[j][x+7] = tmp4;
                            tmp4 = tmp6;

                            // B's old val that need to be moved up have been evicted,
                            // but we had them stored in tmp vars
                            B[j-4][x] = tmp1;
                            B[j-4][x+1] = tmp2;
                            B[j-4][x+2] = tmp3;
                            B[j-4][x+3] = tmp4;
 
                            // Step 3
                            for(i=x+4; (i<x+8) && (i<M); i++ )
                                {
                                    if(x == y && i == j)
                                        {
                                            B[j-4][i] = tmp5;
                                            continue;
                                        }
                                    B[j-4][i] = A[i][j-4];
                                }
                        }
                }
            }
        }
    else
        {
            int i, j, k;
            int MAX_INTS_IN_CACHE = 32*8;
            /* increment - Estimated number of integers which can be used from cache
             * without causing a miss
             */
            int increment;

            if(M < 32 || MAX_INTS_IN_CACHE % M)
                increment = 8;
            else
                increment = MAX_INTS_IN_CACHE / M;


            for(i = 0; i < M; i+= increment){
                for(j = 0; j < N; j++){
                    B[i][j] = A[j][i];

                    for(k = 1; k < increment && i + k < M; k++)
                        B[i + k][j] = A[j][i + k]; 
                }
            }
        } 
}

/* 
 * You can define additional transpose functions below. We've defined
 * a simple one below to help you get started. 
 */ 
char trans_desc[] = "Simple row-wise scan transpose";
void trans(int M, int N, int A[N][M], int B[M][N])
{
    int i, j, tmp;
    for (i = 0; i < N; i++){
        for (j = 0; j < M; j++){
            tmp = A[i][j];
            B[j][i] = tmp;
        }
    }    
}

/*
 * trans_cheat - cheating by using a temporary array on the stack
 */
char trans_cheat_desc[] = "Illegal version that uses stack array";
void trans_cheat(int M, int N, int A[N][M], int B[M][N])
{
    int i, j;
    int C[68][68]; /* Illegal! */
    for (i = 0; i < N; i++){
        for (j = 0; j < M; j++){
            C[i][j] = A[i][j];
        }
    }    
    for (i = 0; i < N; i++){
        for (j = 0; j < M; j++){
            B[i][j] = C[j][i];
        }
    }    
}

char trans_col_desc[] = "column-wise scan transpose";
void trans_col(int M, int N, int A[N][M], int B[M][N]){
    int i, j, tmp;
    for (j = 0; j < M; j++){
        for (i = 0; i < N; i++){
            tmp = A[i][j];
            B[j][i] = tmp;
        }
    }    
}

char zig_zag_desc[] = "using a zig-zag access pattern";
void zig_zag(int M, int N, int A[N][M], int B[M][N]){
    int i, j;
    for (i = 0; i < N; i++){
        for (j = 0; j < M; j++){
            if(i % 2 == 0){
                B[j][i] = A[i][j];
            }else{
                B[M-1-j][i] = A[i][M-1-j];
            }
        }
    }  
}

char trans_blocking_desc[] = "Better locality via blocking";
void trans_blocking(int M, int N, int A[N][M], int B[M][N]){
    int x,y,i,j;
    for(x=0; x<N; x+=8){
        for(y=0; y<M; y+=8){

            for(i=x; (i<x+8) && (i<N) ; i++){
                for(j=y; (j<y+8) && (j<M); j++ ){
                    B[j][i] = A[i][j];
                }
            }

        }
    }
}

char smaller_blocking_desc[] = "Using smaller blocks";
void smaller_blocking(int M, int N, int A[N][M], int B[M][N]){
    int x,y,i,j;
    for(x=0; x<N; x+=4){
        for(y=0; y<M; y+=4){

            for(i=x; (i<x+4) && (i<N) ; i++){
                for(j=y; (j<y+4) && (j<M); j++ ){
                    B[j][i] = A[i][j];
                }
            }

        }
    }

}

// Via experiment, I found that smaller blocking performs better when 
// M=N=64 than M=N=32. Hence we use a variable block size
char variable_blocking_desc[] = "Using variable block size";
void variable_blocking(int M, int N, int A[N][M], int B[M][N]){
    int x,y,i,j,step;
    step = 2*64*64/M/N;
    if (step < 2)
        step=2;
    for(x=0; x<N; x+=step){
        for(y=0; y<M; y+=step){

            for(i=x; (i<x+step) && (i<N) ; i++){
                for(j=y; (j<y+step) && (j<M); j++ ){
                    B[j][i] = A[i][j];
                }
            }

        }
    }

}

// Blocking with zig-zagging access pattern
char zigzag_blocking_desc[] = "variable blocking + zigzag";
void zigzag_blocking(int M, int N, int A[N][M], int B[M][N]){
    int x,y,i,j,step,min;
    step = 2*64*64/M/N;
    if (step < 2)
        step=2;

    for(x=0; x<N; x+=step){
        for(y=0; y<M; y+=step){
            min = y+step < M ? y+step : M;
            for(i=x; (i<x+step) && (i<N) ; i++){
                for(j=y; (j<y+step) && (j<M); j++ ){
                    if(i % 2 == 0){
                        B[j][i] = A[i][j];
                    }else{
                        B[min-1-j+y][i] = A[i][min-1-j+y];
                    }
                }
            }

        }
    }
}

char smaller_blocking_zz_desc[] = "smaller blocks with zig zag";
void smaller_blocking_zz(int M, int N, int A[N][M], int B[M][N]){
    int x,y,i,j,min;
    for(x=0; x<N; x+=4){
        for(y=0; y<M; y+=4){
            min = y+4 < M ? y+4 : M;
            for(i=x; (i<x+4) && (i<N) ; i++){
                for(j=y; (j<y+4) && (j<M); j++ ){
                    if(i % 2 == 0)
                        B[j][i] = A[i][j];
                    else
                        B[min-1-j+y][i] = A[i][min-1-j+y];
                }
            }

        }
    }

}

// This one avoids post diagnal misses
// ONLY WORKS FOR SQUARE MATRICES
char post_diag_desc[] = "avoid post diag miss (requires square)";
void post_diag(int M, int N, int A[N][M], int B[M][N]){
    int i,j,ii,jj;
    int bsize = 4;

    /* if we should start at the front */

    for (ii=0; ii<N; ii+=bsize) { 
        /* copy the items above the diagonal in the block
         * containing the diagonal */
        for (i=ii; i<ii+bsize; i++)
            for (j=i+1; j<ii+bsize; j++)
                B[j][i] = A[i][j];
        /* copy the items below and including the diagonal
         * in the block containing the diagonal */
        for (i=ii; i<ii+bsize; i++)
            for (j=ii; j<i+1; j++)
                B[j][i] = A[i][j];
        /* copy every other block in the row of blocks
         * */
        for (jj=ii+bsize; jj<M+ii; jj+=bsize)  
            for (i=ii; i<ii+bsize; i++)  
                for (j=jj; j<jj+bsize; j++)   
                    B[j % M][i] = A[i][j % M];
    }

}

// This one uses non-square blocks
char nonsquare_blocking_desc[] = "using nonsquare blocks 8*4";
void nonsquare_blocking(int M, int N, int A[N][M], int B[M][N]){
    int x,y,i,j;
    for(x=0; x<N; x+=8){
        for(y=0; y<M; y+=4){

            for(i=x; (i<x+8) && (i<N) ; i++){
                for(j=y; (j<y+4) && (j<M); j++ ){
                    B[j][i] = A[i][j];
                }
            }

        }
    }
}

// non-squre blocks, version 2 using 4*8 blocks
char nonsquare_blocking2_desc[] = "using nonsquare blocks 4*8";
void nonsquare_blocking2(int M, int N, int A[N][M], int B[M][N]){
    int x,y,i,j;
    for(x=0; x<N; x+=4){
        for(y=0; y<M; y+=8){

            for(i=x; (i<x+4) && (i<N) ; i++){
                for(j=y; (j<y+8) && (j<M); j++ ){
                    B[j][i] = A[i][j];
                }
            }

        }
    }
}

char pranav_test_desc[] = "Pranav test";
void pranav_test(int M, int N, int A[N][M], int B[M][N]){
    if(N == 32 && M == 32)
        {
            int x,y,i,j;
            for(x=0; x<N; x+=8)
                {
                    for(y=0; y<M; y+=8)
                        {
                            for(i=x; (i<x+8) && (i<N) ; i++)
                                {
                                    for(j=y; (j<y+8) && (j<M); j++ )
                                        {
                                            if(y == x && i == j) continue; //in diag block
                                            B[j][i] = A[i][j];
                                        }
                                    if(y == x) B[i][i] = A[i][i];
                                }
                        }
                }
        }
    else if(N == 64 && M == 64)
        {
            int x,y,i,j;
            int tmp1, tmp2, tmp3, tmp4;
            for(x=0; x<N; x+=8){
                for(y=0; y<M; y+=8){
                    for(i=x; (i<x+4) && (i<N) ; i++)
                        {
                            for(j=y; (j<y+4) && (j<M); j++ )
                                {
                                    if(i == j)
                                        {
                                            tmp1 = A[i][i];
                                            tmp2 = A[i][i+4];
                                            continue;
                                        }
                                    B[j+4][i+4] = A[i][j];
                                    B[j+4][i] = A[i][j+4];
                                }
                            if(x == y)
                                {
                                    B[i+4][i+4] = tmp1;
                                    B[i+4][i] = tmp2;
                                }
                        }
                    //note i is not being used.. replace tmp4 w/i
                    for(j=y+4; (j<y+8) && (j<M) ; j++)
                        {
                            tmp1 = B[j][x+4];
                            tmp2 = B[j][x+5];
                            tmp3 = B[j][x+6];
                            tmp4 = B[j][x+7];
                            B[j][x+4] = A[x+4][j];
                            B[j][x+5] = A[x+5][j];
                            B[j][x+6] = A[x+6][j];
                            B[j][x+7] = A[x+7][j];
                            B[j-4][x] = tmp1;
                            B[j-4][x+1] = tmp2;
                            B[j-4][x+2] = tmp3;
                            B[j-4][x+3] = tmp4;
                        }
                    for(i=x+4; (i<x+8) && (i<N) ; i++)
                        {
                            for(j=y; (j<y+4) && (j<M); j++ )
                                {
                                    B[j][i] = A[i][j];
                                }
                        }
                }
            }
        }
}

/*
 * registerFunctions - This function registers your transpose
 *     functions with the driver.  At runtime, the driver will
 *     evaluate each of the registered functions and summarize their
 *     performance. This is a handy way to experiment with different
 *     transpose strategies.
 */
void registerFunctions()
{
    /* Register your solution function */
    registerTransFunction(transpose_submit, transpose_submit_desc);

    /* Register any additional transpose functions */
    registerTransFunction(trans, trans_desc);
    //  registerTransFunction(trans_cheat, trans_cheat_desc);
    registerTransFunction(trans_col, trans_col_desc);
    registerTransFunction(zig_zag, zig_zag_desc);
    //  registerTransFunction(trans_blocking, trans_blocking_desc);
    //  registerTransFunction(smaller_blocking, smaller_blocking_desc);
    //  registerTransFunction(variable_blocking, variable_blocking_desc);
    //  registerTransFunction(zigzag_blocking, zigzag_blocking_desc);
    //  registerTransFunction(smaller_blocking_zz, smaller_blocking_zz_desc);
    //  registerTransFunction(post_diag, post_diag_desc);
    //  registerTransFunction(nonsquare_blocking, nonsquare_blocking_desc);
    //  registerTransFunction(nonsquare_blocking2, nonsquare_blocking2_desc);
    //  registerTransFunction(pranav_test, pranav_test_desc);
}


/* 
 * is_transpose - This optional helper function checks if B is the
 * transpose of A. You can check the correctness of your transpose by
 * calling it before returning from the transpose function.
 */
int is_transpose(int M, int N, int A[N][M], int B[M][N]){
    int i, j;

    for (i = 0; i < N; i++){
        for(j = 0; j < M; ++j){
            if (A[i][j] != B[j][i]) {
                return 0;
            }
        }
    }
    return 1;
}

```

``` C
/*
 * test-trans.c - Checks the correctness and performance of all of the
 *     student's transpose functions and records the results for their
 *     official submitted version as well.
 */
#include <stdio.h>
#include <stdlib.h>
#include <assert.h>
#include <unistd.h>
#include <string.h>
#include <signal.h>
#include <getopt.h>
#include <sys/types.h>
#include "cachelab.h"
#include <sys/wait.h> // fir WEXITSTATUS
#include <limits.h> // for INT_MAX

/* Maximum array dimension */
#define MAXN 256

/* The description string for the transpose_submit() function that the
   student submits for credit */
#define SUBMIT_DESCRIPTION "Transpose submission"

/* External function defined in trans.c */
extern void registerFunctions();

/* External variables defined in cachelab-tools.c */
extern trans_func_t func_list[MAX_TRANS_FUNCS];
extern int func_counter; 

/* Globals set on the command line */
static int M = 0;
static int N = 0;

/* The correctness and performance for the submitted transpose function */
struct results {
    int funcid;
    int correct;
    int misses;
};
static struct results results = {-1, 0, INT_MAX};

/* 
 * eval_perf - Evaluate the performance of the registered transpose functions
 */
void eval_perf(unsigned int s, unsigned int E, unsigned int b)
{
    int i,flag;
    unsigned int len, hits, misses, evictions;
    unsigned long long int marker_start, marker_end, addr;
    char buf[1000], cmd[255];
    char filename[128];

    registerFunctions(); 

    /* Open the complete trace file */
    FILE* full_trace_fp;  
    FILE* part_trace_fp; 

    /* Evaluate the performance of each registered transpose function */

    for (i=0; i<func_counter; i++) {
        if (strcmp(func_list[i].description, SUBMIT_DESCRIPTION) == 0 )
            results.funcid = i; /* remember which function is the submission */


        printf("\nFunction %d (%d total)\nStep 1: Validating and generating memory traces\n",i,func_counter);
        /* Use valgrind to generate the trace */

        sprintf(cmd, "valgrind --tool=lackey --trace-mem=yes --log-fd=1 -v ./tracegen -M %d -N %d -F %d  > trace.tmp", M, N,i);
        flag=WEXITSTATUS(system(cmd));
        if (0!=flag) {
            printf("Validation error at function %d! Run ./tracegen -M %d -N %d -F %d for details.\nSkipping performance evaluation for this function.\n",flag-1,M,N,i);      
            continue;
        }

        /* Get the start and end marker addresses */
        FILE* marker_fp = fopen(".marker", "r");
        assert(marker_fp);
        fscanf(marker_fp, "%llx %llx", &marker_start, &marker_end);
        fclose(marker_fp);


        func_list[i].correct=1;

        /* Save the correctness of the transpose submission */
        if (results.funcid == i ) {
            results.correct = 1;
        }

        full_trace_fp = fopen("trace.tmp", "r");
        assert(full_trace_fp);


        /* Filtered trace for each transpose function goes in a separate file */
        sprintf(filename, "trace.f%d", i);
        part_trace_fp = fopen(filename, "w");
        assert(part_trace_fp);
    
        /* Locate trace corresponding to the trans function */
        flag = 0;
        while (fgets(buf, 1000, full_trace_fp) != NULL) {

            /* We are only interested in memory access instructions */
            if (buf[0]==' ' && buf[2]==' ' &&
                (buf[1]=='S' || buf[1]=='M' || buf[1]=='L' )) {
                sscanf(buf+3, "%llx,%u", &addr, &len);
        
                /* If start marker found, set flag */
                if (addr == marker_start)
                    flag = 1;

                /* Valgrind creates many spurious accesses to the
                   stack that have nothing to do with the students
                   code. At the moment, we are ignoring all stack
                   accesses by using the simple filter of recording
                   accesses to only the low 32-bit portion of the
                   address space. At some point it would be nice to
                   try to do more informed filtering so that would
                   eliminate the valgrind stack references while
                   include the student stack references. */
                if (flag && addr < 0xffffffff) {
                    fputs(buf, part_trace_fp);
                }

                /* if end marker found, close trace file */
                if (addr == marker_end) {
                    flag = 0;
                    fclose(part_trace_fp);
                    break;
                }
            }
        }
        fclose(full_trace_fp);

        /* Run the reference simulator */
        printf("Step 2: Evaluating performance (s=%d, E=%d, b=%d)\n", s, E, b);
        char cmd[255];
        sprintf(cmd, "./csim-ref -s %u -E %u -b %u -t trace.f%d > /dev/null", 
                s, E, b, i);
        system(cmd);
    
        /* Collect results from the reference simulator */
        FILE* in_fp = fopen(".csim_results","r");
        assert(in_fp);
        fscanf(in_fp, "%u %u %u", &hits, &misses, &evictions);
        fclose(in_fp);
        func_list[i].num_hits = hits;
        func_list[i].num_misses = misses;
        func_list[i].num_evictions = evictions;
        printf("func %u (%s): hits:%u, misses:%u, evictions:%u\n",
               i, func_list[i].description, hits, misses, evictions);
    
        /* If it is transpose_submit(), record number of misses */
        if (results.funcid == i) {
            results.misses = misses;
        }
    }
  
}

/*
 * usage - Print usage info
 */
void usage(char *argv[]){
    printf("Usage: %s [-h] -M <rows> -N <cols>\n", argv[0]);
    printf("Options:\n");
    printf("  -h          Print this help message.\n");
    printf("  -M <rows>   Number of matrix rows (max %d)\n", MAXN);
    printf("  -N <cols>   Number of  matrix columns (max %d)\n", MAXN);
    printf("Example: %s -M 8 -N 8\n", argv[0]);       
}

/*
 * sigsegv_handler - SIGSEGV handler
 */
void sigsegv_handler(int signum){
    printf("Error: Segmentation Fault.\n");
    printf("TEST_TRANS_RESULTS=0:0\n");
    fflush(stdout);
    exit(1);
}

/*
 * sigalrm_handler - SIGALRM handler
 */
void sigalrm_handler(int signum){
    printf("Error: Program timed out.\n");
    printf("TEST_TRANS_RESULTS=0:0\n");
    fflush(stdout);
    exit(1);
}

/* 
 * main - Main routine
 */
int main(int argc, char* argv[])
{
    char c;

    while ((c = getopt(argc,argv,"M:N:h")) != -1) {
        switch(c) {
        case 'M':
            M = atoi(optarg);
            break;
        case 'N':
            N = atoi(optarg);
            break;
        case 'h':
            usage(argv);
            exit(0);
        default:
            usage(argv);
            exit(1);
        }
    }
  
    if (M == 0 || N == 0) {
        printf("Error: Missing required argument\n");
        usage(argv);
        exit(1);
    }

    if (M > MAXN || N > MAXN) {
        printf("Error: M or N exceeds %d\n", MAXN);
        usage(argv);
        exit(1);
    }

    /* Install SIGSEGV and SIGALRM handlers */
    if (signal(SIGSEGV, sigsegv_handler) == SIG_ERR) {
        fprintf(stderr, "Unable to install SIGALRM handler\n");
        exit(1);
    }

    if (signal(SIGALRM, sigalrm_handler) == SIG_ERR) {
        fprintf(stderr, "Unable to install SIGALRM handler\n");
        exit(1);
    }

    /* Time out and give up after a while */
    alarm(120);

    /* Check the performance of the student's transpose function */
    eval_perf(5, 1, 5);
  
    /* Emit the results for this particular test */
    if (results.funcid == -1) {
        printf("\nError: We could not find your transpose_submit() function\n");
        printf("Error: Please ensure that description field is exactly \"%s\"\n", 
               SUBMIT_DESCRIPTION);
        printf("\nTEST_TRANS_RESULTS=0:0\n");
    }
    else {
        printf("\nSummary for official submission (func %d): correctness=%d misses=%d\n",
               results.funcid, results.correct, results.misses);
        printf("\nTEST_TRANS_RESULTS=%d:%d\n", results.correct, results.misses);
    }
    return 0;
}
```
