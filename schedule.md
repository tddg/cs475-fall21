---
layout: page
title: "Course Schedule"
permalink: /schedule.html
---

<style>
table.calendar {
    font-family: arial, helvetica;
    font-size: 10pt;
    empty-cells: show;
    border: 1px solid #000000;
    border-collapse: collapse;
}
table.calendar tr td {
    border: 1px solid #aaaaaa;
}
table.calendar tr {
    vertical-align: top;
    height: 5em;
    background: #ffffff;
}
table.calendar thead tr {
    text-align: center;
    background: #444444;
    color: #ffffff;
    height: auto;
    font-weight: bold;
}
/*.date {
	background: Gainsboro;
}*/
.holiday {
    background: #F0FFF0;
}
.lecture {
    background: #ffffaa;
}
.fundamentals {
    background: Moccasin;
}
/*.concurrency {
    background: LightGreen;
}*/
.fault-tolerance {
    background: MediumSpringGreen;
}
.consistency {
    background: Plum;
}
.advanced {
    background: Aqua;
}
.presentation {
    background: Plum;
}
.exam {
    background: DarkOrange;
}
.important {
    background: Bisque;
}
.nodue {
    background: #FFFAFA;
}
.optional {
    background: Linen;
}
.reading {
    color: Black;
}
.hw {
    background: #ffaaaa;
	color: Black;
	font-weight: bold;
}
.hwdue {
    color: #ff0000;
    background: #ffaaaa;
	font-weight: bold;
}
.assignment {
    color: #0aa00a;
	font-weight: bold;
}
.date {
	background: #eeeeee;
    color: #444444;
}
</style>

The course schedule is tentative and subject to change\*.

<p>
<table class="calendar" cellspacing="0" cellpadding="6" width="100%">
 <thead>
  <tr>
   <td width="10%">Date</td><td width="45%">Topics</td>
   <td width="30%">Readings</td><td width="15%">Notes</td>
  </tr>
 </thead>

<tr> <!-- week of Aug 23 -->
  <td id="2021-8-24" class="date"><b>Tue 08/24</b></td>
  <td class="lecture">
		<b>Lec 1:</b> Introduction [<a href="./public/lecs/lec1-intro.pdf">slides</a>]
		[<a href="./public/lecs/lec1-intro+notes.pdf">slides+notes</a>]
  </td>
  <td class="reading"></td>
  <td class="nodue"></td>
</tr>
<tr> <!-- week of Aug 23 -->
  <td id="2021-8-26" class="date"><b>Thu 08/26</b></td>
  <td class="lecture">
		Go system programming [<a href="./public/lecs/go_basics.pdf">slides</a>] 
		[<a href="./public/lecs/go_handout.docx">handout1</a>] 
		[<a href="./public/lecs/go_handout2.docx">handout2</a>]
  </td>
  <td class="reading"></td>
  <td class="hw">
		<a href="./lab0.html">Lab 0</a> out
  </td>
</tr>

<tr> <!-- week of Aug 30 -->
  <td id="2021-8-31" class="date"><b>Tue 08/31</b></td>
  <td class="fundamentals">
		<b>Lec 2:</b> Concurrency overview
  </td>
  <td class="reading">
		OSTEP: <a href="https://pages.cs.wisc.edu/~remzi/OSTEP/cpu-intro.pdf">Processes</a>, 
		<a href="https://pages.cs.wisc.edu/~remzi/OSTEP/threads-api.pdf">Threads</a>, 
		<a href="https://pages.cs.wisc.edu/~remzi/OSTEP/threads-intro.pdf">Concurrency intro</a>, 
		<a href="https://pages.cs.wisc.edu/~remzi/OSTEP/threads-locks.pdf">Locks</a>
  </td>
  <td class="nodue"></td>
</tr>
<tr> <!-- week of Aug 30 -->
  <td id="2021-09-02" class="date"><b>Thu 09/02</b></td>
  <td class="fundamentals">
		Networking, RPC
  </td>
  <td class="reading">
		van Steen &sect;4.1-4.2; $sect;8.3 (optional)
  </td>
  <td class="hwdue">
		Lab 0 due
  </td>
</tr>

<tr> <!-- week of Sep 06 -->
  <td id="2021-09-07" class="date"><b>Tue 09/07</b></td>
  <td class="fundamentals">
		MapReduce
  </td>
  <td class="reading">
		<a href="./public/papers/mapreduce_osdi04.pdf">MapReduce</a> paper (required)
  </td>
  <td class="hw">
		Lab 1 out
  </td>
</tr>
<tr> <!-- week of Sep 06 -->
  <td id="2021-09-09" class="date"><b>Thu 09/09</b></td>
  <td class="fundamentals">
		Google File System
  </td>
  <td class="reading">
		<a href="./public/papers/gfs_sosp03.pdf">GFS</a> paper (required)
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Sep 13 -->
  <td id="2021-09-14" class="date"><b>Tue 09/14</b></td>
  <td class="fundamentals">
		Time and clocks I
  </td>
  <td class="reading">
		Time, clocks paper, <br/>
		van Steen &sect;6.1-6.2
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Sep 13 -->
  <td id="2021-09-16" class="date"><b>Thu 09/16</b></td>
  <td class="fundamentals">
		Time and clocks II
  </td>
  <td class="reading">
		Time, clocks paper, <br/>
		van Steen &sect;6.1-6.2
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Sep 20 -->
  <td id="2021-09-21" class="date"><b>Tue 09/21</b></td>
  <td class="fault-tolerance">
		 2PC, safety, liveness
  </td>
  <td class="reading">
		van Steen &sect;8.5
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Sep 20 -->
  <td id="2021-09-23" class="date"><b>Thu 09/23</b></td>
  <td class="fault-tolerance">
		Consensus w/ Paxos
  </td>
  <td class="reading">
		Paxos made simple paper,<br/>
		van Steen &sect;8.1-8.2
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Sep 27 -->
  <td id="2021-09-28" class="date"><b>Tue 09/28</b></td>
  <td class="fault-tolerance">
		 Raft
  </td>
  <td class="reading">
		Raft paper (extended version)
  </td>
  <td class="hwdue">
		Lab 1 due
  </td>
</tr>
<tr> <!-- week of Sep 27 -->
  <td id="2021-09-30" class="date"><b>Thu 09/30</b></td>
  <td class="fault-tolerance">
		More on Raft 
  </td>
  <td class="reading">
		Raft paper (extended version)
  </td>
  <td class="hw">
		Lab 2 out
  </td>
</tr>

<tr> <!-- week of Oct 04 -->
  <td id="2021-10-05" class="date"><b>Tue 10/05</b></td>
  <td class="lecture">
		 Midterm review
  </td>
  <td class="reading">
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Oct 04 -->
  <td id="2021-10-07" class="date"><b>Thu 10/07</b></td>
  <td class="exam">
		Midterm
  </td>
  <td class="reading">
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Oct 11 -->
  <td id="2021-10-12" class="date"><b>Tue 10/12</b></td>
  <td class="holiday">
		 Fall break!
  </td>
  <td class="reading">
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Oct 11 -->
  <td id="2021-10-14" class="date"><b>Thu 10/14</b></td>
  <td class="fault-tolerance">
		Byzantine fault tolerance
  </td>
  <td class="reading">
  </td>
  <td class="hwdue">
		Lab 2a due
  </td>
</tr>

<tr> <!-- week of Oct 18 -->
  <td id="2021-10-19" class="date"><b>Tue 10/19</b></td>
  <td class="consistency">
		Strong consistency and CAP theorem
  </td>
  <td class="reading">
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Oct 18 -->
  <td id="2021-10-21" class="date"><b>Thu 10/21</b></td>
  <td class="consistency">
		DHT, Chord
  </td>
  <td class="reading">
		Chord paper, <br/>
		van Steen &sect;5.2 and &sect;2.3
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Oct 25 -->
  <td id="2021-10-26" class="date"><b>Tue 10/26</b></td>
  <td class="consistency">
		Eventual consistency, Dynamo
  </td>
  <td class="reading">
		Dynamo paper
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Oct 25 -->
  <td id="2021-10-28" class="date"><b>Thu 10/28</b></td>
  <td class="consistency">
		Concurrency control, locking
  </td>
  <td class="reading">
  </td>
  <td class="hwdue">
		Lab 2b due
  </td>
</tr>

<tr> <!-- week of Nov 1 -->
  <td id="2021-11-02" class="date"><b>Tue 11/02</b></td>
  <td class="consistency">
		OCC, MVCC
  </td>
  <td class="reading">
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Nov 1 -->
  <td id="2021-11-04" class="date"><b>Thu 11/04</b></td>
  <td class="advanced">
		Spark
  </td>
  <td class="reading">
		Spark paper
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Nov 8 -->
  <td id="2021-11-09" class="date"><b>Tue 11/09</b></td>
  <td class="advanced">
		Facebook Memcache
  </td>
  <td class="reading">
		Scaling memcache paper
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Nov 8 -->
  <td id="2021-11-11" class="date"><b>Thu 11/11</b></td>
  <td class="advanced">
		Datacenter as a computer
  </td>
  <td class="reading">
		Datacenter as a computer paper
  </td>
  <td class="hwdue">
		Lab 2c due
		<p style="color:black">Lab 3 out</p>
  </td>
</tr>

<tr> <!-- week of Nov 15 -->
  <td id="2021-11-16" class="date"><b>Tue 11/16</b></td>
  <td class="advanced">
		Cloud computing
  </td>
  <td class="reading">
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Nov 15 -->
  <td id="2021-11-18" class="date"><b>Thu 11/18</b></td>
  <td class="advanced">
		Serverless computing
  </td>
  <td class="reading">
		XXX
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Nov 22 -->
  <td id="2021-11-23" class="date"><b>Tue 11/23</b></td>
  <td class="advanced">
		Data-intensive cluster computing in milliseconds
  </td>
  <td class="reading">
		MilliSort and MilliQuery paper
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Nov 22 -->
  <td id="2021-11-25" class="date"><b>Thu 11/25</b></td>
  <td class="holiday">
		Thanksgiving break!
  </td>
  <td class="reading">
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Nov 29 -->
  <td id="2021-11-30" class="date"><b>Tue 11/30</b></td>
  <td class="advanced">
		Reasoning about system performance
  </td>
  <td class="reading">
  </td>
  <td class="hwdue">
		Lab 3 due
  </td>
</tr>
<tr> <!-- week of Nov 29 -->
  <td id="2021-12-02" class="date"><b>Thu 12/02</b></td>
  <td class="lecture">
		Final review
  </td>
  <td class="reading">
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Dec 06 -->
  <td id="2021-12-07" class="date"><b>Tue 12/07</b></td>
  <td class="holiday">
		Reading day
  </td>
  <td class="reading">
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Dec 06 -->
  <td id="2021-12-09" class="date"><b>Thu 12/09</b></td>
  <td class="exam">
		Final exam
  </td>
  <td class="reading">
  </td>
  <td class="nodue">
  </td>
</tr>

</table>

</p>

<p style='font-size:12pt'>&#42;: Color codings:
<table style='font-size:12pt'>
<tr> 
	<td class="fundamentals"> -Fundamentals- </td>
	<td class="fault-tolerance"> -Fault tolerance- </td>
	<td class="consistency"> -Consistency, scalability, transactions- </td>
	<td class="advanced"> -Boutique topics- </td>
</tr>
</table>
