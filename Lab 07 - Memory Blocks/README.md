# <p align="center">Memory Blocks</p>

// anchor abstract

---

// anchor intro 

## Procedure & Implementation

// anchor

<details>
  <summary>VHDL Code</summary>
<br>

```VHDL


```

</details>


<details>
  <summary>Practical Results</summary>
	
<be>

 <p align="center">	 
  <img src="Photos/workaround-reset-case.jpg" title="0000 1000 = 08"/>
</p>

// anchor

<p align="center">
  <img src="Photos/writing-5-into-00address-in-memory.jpg" style="width: 49%; height: 300px;" title=""/> <img src="Photos/reading-from-00address-in-memory.jpg.jpg" style="width: 49%; height: 300px;" title="" /> 
 </p>

// anchor

 <p align="center">
  <img src="Photos/writing-8-into-01address-in-memory.jpg" style="width: 49%; height: 300px;" title=""/>  <img src="Photos/reading-from-01address-in-memory.jpg.jpg" style="width: 49%; height: 300px;" title="0000 0011 = 03" />
 </p>

// anchor

 <p align="center">
  <img src="Photos/checking-a-memory-location-we-didnt-write-anything-to.jpg" style="width: 49%; height: 300px;" title="0000 0100 = 04"/> <img src="Photos/wroteandread-value-in11adress.jpg" style="width: 49%; height: 300px;" title="0000 0101 = 05" />
<img src="Photos/overwriting-by-writing-a-new-value-into-same-address.jpg" style="width: 49%; height: 300px;" title="0000 0110 = 06"/>  <img src="Photos/confirming-the-overwrite-by-reading-from-address.jpg" style="width: 49%; height: 300px;" title="0000 0111 = 07" />
 </p>

// anchor
  
 <p align="center">
<img src="Photos/reading-proofthat5issavedinthisaddressregardlessofthedatainnow.jpg" style="width: 49%; height: 300px;" title="0000 1000 = 08"/> <img src="Photos/reading-proofthat8issavedinthisaddressregardlessofthedatainnow.jpg" style="width: 49%; height: 300px;" title="0000 1001 = 09" />
</p>

// anchor

 <p align="center">	 
  <img src="Photos/workaround-reset-case.jpg" title="0000 1000 = 08"/>
</p>

// anchor
	
</details>

<details>
  <summary>Simulation Results</summary>
	
<br>

<p align="center">
  <img src="Photos/waveform.png" title="anchor" />
</p>

// anchor

</details>

## Conclusion

// anchor

## Resources
|3| Ashenden, P. J. (2008). The designer’s guide to VHDL (3rd ed). Morgan Kaufmann Publishers.    

<br>

```mermaid
gantt
    title Work Division Gantt Chart
    tickInterval 1day
    todayMarker off
    axisFormat %a-%Y-%m-%d
    section Preparation         
        Nour Mostafa : active, 2024-11-05 00:00, 01h
        Mohamed Abouissa : 2024-11-05 00:00, 01h
    section Quartus         
        Nour Mostafa : active, 2024-11-06 00:00, 1d
        Mohamed Abouissa : 2024-11-06 00:00, 1d
    section Waveform       
        Nour Mostafa : active, 2024-11-06 00:00, 1d
        Mohamed Abouissa : 2024-11-06 00:00, 1d
    section Results       
        Nour Mostafa : active, 2024-11-06 00:00, 1d
        Mohamed Abouissa : 2024-11-06 00:00, 1d
    section Report
        Nour Mostafa : active, 2024-11-7 00:00, 2d
        Mohamed Abouissa : 2024-11-11 00:00, 2d
```

We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback which has significantly contributed to the successful completion of this experiment.

This publication adheres to all regulatory laws and guidelines established by the American University of Ras Al Khaimah (AURAK) regarding the dissemination of academic materials.
