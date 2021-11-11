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
   <td width="10%">Date</td><td width="48%">Topics</td>
   <td width="30%">Readings</td><td width="12%">Notes</td>
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
		Go system programming [<a href="./public/lecs/go_basics_v2.pdf">slides</a>] 
		[<a href="./public/lecs/go_handout.docx">handout1</a>] 
		[<a href="./public/lecs/go_handout2.docx">handout2</a>]
  </td>
  <td class="reading">
		<a href="https://tour.golang.org/welcome/1">Online Go tutorial</a> (optional)
  </td>
  <td class="hw">
		<a href="./lab0.html">Lab 0</a> out
  </td>
</tr>

<tr> <!-- week of Aug 30 -->
  <td id="2021-8-31" class="date"><b>Tue 08/31</b></td>
  <td class="fundamentals">
		<b>Lec 2:</b> Concurrency overview [<a href="./public/lecs/lec2-concurrency.pdf">slides</a>]
		[<a href="./public/lecs/lec2-concurrency+notes.pdf">slides+notes</a>]
  </td>
  <td class="reading">
		OSTEP: <a href="https://pages.cs.wisc.edu/~remzi/OSTEP/cpu-intro.pdf">Processes</a>, 
		<a href="https://pages.cs.wisc.edu/~remzi/OSTEP/threads-api.pdf">Threads</a>, 
		<a href="https://pages.cs.wisc.edu/~remzi/OSTEP/threads-intro.pdf">Concurrency intro</a>, 
		<a href="https://pages.cs.wisc.edu/~remzi/OSTEP/threads-locks.pdf">Locks</a> (required)
  </td>
  <td class="nodue"></td>
</tr>
<tr> <!-- week of Aug 30 -->
  <td id="2021-09-02" class="date"><b>Thu 09/02</b></td>
  <td class="fundamentals">
		<b>Lec 3:</b> Networking, RPC [<a href="./public/lecs/lec3-rpc.pdf">slides</a>]
  </td>
  <td class="reading">
		van Steen &sect;4.1-4.2; &sect;8.3 (optional)
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Sep 06 -->
  <td id="2021-09-07" class="date"><b>Tue 09/07</b></td>
  <td class="fundamentals">
		<b>Lec 4:</b> MapReduce [<a href="./public/lecs/lec4-mapreduce.pdf">slides</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/mapreduce_osdi04.pdf">MapReduce</a> paper (required)
  </td>
  <td class="hw">
		<a href="./lab0.html">Lab 0</a> due <br/>
		<a href="./lab1.html">Lab 1</a> out
  </td>
</tr>
<tr> <!-- week of Sep 06 -->
  <td id="2021-09-09" class="date"><b>Thu 09/09</b></td>
  <td class="fundamentals">
		<b>Lec 5-1:</b> MapReduce II, RPCs in Go [<a href="./public/lecs/lec5-mr-ft.pdf">slides</a>] <br/>
		<b>Lab 1 overview</b> [<a href="https://edstem.org/us/courses/8902/discussion/601030">video</a> (Ed post)] [<a href="./public/lecs/lab1_mapreduce_arch.pdf">notes</a>] <br/>
		<b>Lec 5-2:</b> Google File System [<a href="./public/lecs/lec5-gfs.pdf">slides</a>] 
	[<a href="./public/lecs/lec5-gfs+notes.pdf">slides+notes</a>]
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
		<b>Lec 6:</b> Time and clocks I [<a href="./public/lecs/lec6-time.pdf">slides</a> (pdf)]
	[<a href="./public/lecs/lec6-time+notes.pdf">pdf+notes</a>]
	[<a href="./public/lecs/lec6-time.pptx">slides</a> (pptx)]
	[<a href="./public/lecs/lec6-time+notes.pptx">pptx+notes</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/time.pdf">Time, clocks</a> paper (optional), <br/>
		van Steen &sect;6.1-6.2 (optional)
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Sep 13 -->
  <td id="2021-09-16" class="date"><b>Thu 09/16</b></td>
  <td class="fundamentals">
		<b>Lec 6-2:</b> Time and clocks II [<a href="./public/lecs/lec6-time.pdf">slides</a> (pdf)]
	[<a href="./public/lecs/lec6-time+notes.pdf">pdf+notes</a>]
	[<a href="./public/lecs/lec6-time.pptx">slides</a> (pptx)]
	[<a href="./public/lecs/lec6-time+notes.pptx">pptx+notes</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/time.pdf">Time, clocks</a> paper (optional), <br/>
		van Steen &sect;6.1-6.2 (optional)
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Sep 20 -->
  <td id="2021-09-21" class="date"><b>Tue 09/21</b></td>
  <td class="fault-tolerance">
		<b>Lec 7:</b> 2PC, safety, liveness [<a href="./public/lecs/lec7-2pc.pdf">slides</a>]
  </td>
  <td class="reading">
		van Steen &sect;8.5 (optional)
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Sep 20 -->
  <td id="2021-09-23" class="date"><b>Thu 09/23</b></td>
  <td class="fault-tolerance">
		<b>Lec 8:</b> Consensus: Paxos [<a href="./public/lecs/paxos.pdf">slides</a>] [<a href="./public/lecs/paxos+notes.pdf">slides+notes</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/paxos_simple.pdf">Paxos</a> paper (required),<br/>
		watch <a href="https://youtu.be/JEpsBg0AO6o">Paxos video</a> (required),<br/>
		<a href="./public/papers/paxos_vs_raft.pdf">Paxos vs. Raft</a> paper: Page 7 (optional), <br/>
		van Steen &sect;8.1-8.2 (optional)
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Sep 27 -->
  <td id="2021-09-28" class="date"><b>Tue 09/28</b></td>
  <td class="fault-tolerance">
		<b>Lec 9:</b> Consensus: Raft [<a href="./public/lecs/raft.pdf">slides</a>] [<a href="./public/lecs/raft+notes.pdf">slides+notes</a>] <br/>
		How to read a paper [<a href="./public/lecs/how_to_read_a_paper.pdf">slides</a>] [<a href="https://edstem.org/us/courses/8902/discussion/674977">video</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/raft.pdf">Raft</a> paper (required), <br/>
		watch <a href="https://youtu.be/YbZ3zDzDnrw">Raft video</a> (required) <br/>
		<a href="https://raft.github.io/">Raft visualization </a>
  </td>
  <td class="hwdue">
		<a href="./lab1.html">Lab 1</a> due
  </td>
</tr>
<tr> <!-- week of Sep 27 -->
  <td id="2021-09-30" class="date"><b>Thu 09/30</b></td>
  <td class="fault-tolerance">
		<b>Lec 9-2:</b> Consensus: Raft II [<a href="./public/lecs/raft.pdf">slides</a>] [<a href="./public/lecs/raft+notes.pdf">slides+notes</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/raft.pdf">Raft</a> paper (required), <br/>
		watch <a href="https://youtu.be/YbZ3zDzDnrw">Raft video</a> (required) <br/>
		<a href="https://raft.github.io/">Raft visualization </a>
  </td>
  <td class="hw">
		<a href="./lab2.html">Lab 2a</a> out
  </td>
</tr>

<tr> <!-- week of Oct 04 -->
  <td id="2021-10-05" class="date"><b>Tue 10/05</b></td>
  <td class="lecture">
		 Midterm review [<a href="./public/lecs/midterm-review.pdf">slides</a>] [<a href="./public/lecs/midterm-review+notes.pdf">slides+notes</a>]
  </td>
  <td class="reading">
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Oct 04 -->
  <td id="2021-10-07" class="date"><b>Thu 10/07</b></td>
  <td class="exam">
		Midterm (<a href="./public/midterm_stats.png">stats</a>)
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
		<b>Lec 10:</b> Byzantine fault tolerance [<a href="./public/lecs/lec10-bft.pdf">slides</a> (pdf)] [<a href="./public/lecs/lec10-bft+notes.pdf">pdf+notes</a>] [<a href="./public/lecs/lec10-bft.pptx">slides</a> (pptx)] [<a href="./public/lecs/lec10-bft+notes.pptx">pptx+notes</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/pbft.pdf">PBFT</a> paper: &sect;1-4 (optional)
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Oct 18 -->
  <td id="2021-10-19" class="date"><b>Tue 10/19</b></td>
  <td class="consistency">
		<b>Lec 11:</b> Strong consistency [<a href="./public/lecs/lec11-strong-consistency.pdf">slides</a> (pdf)] 
		[<a href="./public/lecs/lec11-strong-consistency+notes.pdf">pdf+notes</a>] 
		[<a href="./public/lecs/lec11-strong-consistency.pptx">slides</a> (pptx]
		[<a href="./public/lecs/lec11-strong-consistency+notes.pptx">pptx+notes</a>] <br/>
		Linearizability examples [<a href="./public/lecs/linearizability_examples.pdf">notes</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/linearizability.pdf">Linearizability</a> paper: &sect;1-2 (required) <br/>
		<a href="./public/papers/rifl.pdf">RIFL</a> paper: &sect;2 (optional)
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Oct 18 -->
  <td id="2021-10-21" class="date"><b>Thu 10/21</b></td>
  <td class="consistency">
		<b>Lec 12:</b> CAP Theorem and causal consistency 
		[<a href="./public/lecs/lec12-cap.pdf">slides</a> (pdf)] 
		[<a href="./public/lecs/lec12-cap+notes.pdf">pdf+notes</a>] 
		[<a href="./public/lecs/lec12-cap.pptx">slides</a> (pptx)] 
		[<a href="./public/lecs/lec12-cap+notes.pptx">pptx+notes</a>] 
  </td>
  <td class="reading">
		<a href="http://dbmsmusings.blogspot.com/2010/04/problems-with-cap-and-yahoos-little.html">Problems with CAP</a> blog (optional)
  </td>
  <td class="hwdue">
		<a href="./lab2.html">Lab 2a</a> due
  </td>
</tr>

<tr> <!-- week of Oct 25 -->
  <td id="2021-10-26" class="date"><b>Tue 10/26</b></td>
  <td class="consistency">
		<b>Lec 13:</b> Consistent hashing, Dynamo 
		[<a href="./public/lecs/lec13-dynamo.pdf">slides</a> (pdf)]
		[<a href="./public/lecs/lec13-dynamo+notes.pdf">pdf+notes</a>] 
		[<a href="./public/lecs/lec13-dynamo.pptx">slides</a> (pptx)] 
		[<a href="./public/lecs/lec13-dynamo+notes.pptx">pptx+notes</a>] 
  </td>
  <td class="reading">
		<a href="./public/papers/dynamo_sosp07.pdf">Dynamo</a> paper (required)
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Oct 25 -->
  <td id="2021-10-28" class="date"><b>Thu 10/28</b></td>
  <td class="consistency">
		<b>Lec 13-2:</b> Consistent hashing, Dynamo
		[<a href="./public/lecs/lec13-dynamo.pdf">slides</a> (pdf)]
		[<a href="./public/lecs/lec13-dynamo+notes.pdf">pdf+notes</a>] 
		[<a href="./public/lecs/lec13-dynamo.pptx">slides</a> (pptx)] 
		[<a href="./public/lecs/lec13-dynamo+notes.pptx">pptx+notes</a>] <br/>
		Hinted handoff consistency example (Slide#57) [<a href="./public/lecs/hinted_handoff.pdf">notes</a>] <br/>
		VV application-resolving case (Slide#70) [<a href="./public/lecs/app_resolving_timeline.pdf">notes</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/dynamo_sosp07.pdf">Dynamo</a> paper (required)
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Nov 1 -->
  <td id="2021-11-02" class="date"><b>Tue 11/02</b></td>
  <td class="consistency">
		<b>Lec 14:</b> Concurrency control, recovery, and locking 
		[<a href="./public/lecs/lec14-cc.pdf">slides</a> (pdf)]
		[<a href="./public/lecs/lec14-cc+notes.pdf">pdf+notes</a>] 
		[<a href="./public/lecs/lec14-cc.pptx">slides</a> (pptx)] 
		[<a href="./public/lecs/lec14-cc+notes.pptx">pptx+notes</a>] <br/>
		Phantom problem (Slide#43) [<a href="./public/lecs/phantom_row.pdf">notes</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/franklin97.pdf">Franklin</a> paper: &sect;1-3.1 (required)
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Nov 1 -->
  <td id="2021-11-04" class="date"><b>Thu 11/04</b></td>
  <td class="consistency">
		<b>Lec 14-2:</b> Concurrency control, recovery, and locking 
		[<a href="./public/lecs/lec14-cc.pdf">slides</a> (pdf)]
		[<a href="./public/lecs/lec14-cc+notes.pdf">pdf+notes</a>] 
		[<a href="./public/lecs/lec14-cc.pptx">slides</a> (pptx)] 
		[<a href="./public/lecs/lec14-cc+notes.pptx">pptx+notes</a>] <br/>
  </td>
  <td class="reading">
		<a href="./public/papers/franklin97.pdf">Franklin</a> paper: &sect;1-3.1 (required)
  </td>
  <td class="hwdue">
		<a href="./lab2.html">Lab 2b</a> due
  </td>
</tr>

<tr> <!-- week of Nov 8 -->
  <td id="2021-11-09" class="date"><b>Tue 11/09</b></td>
  <td class="consistency">
		<b>Lec 15:</b> 2PL, OCC 
		[<a href="./public/lecs/lec15-2pl-occ.pdf">slides</a> (pdf)]
		[<a href="./public/lecs/lec15-2pl-occ+notes.pdf">pdf+notes</a>] 
		[<a href="./public/lecs/lec15-2pl-occ.pptx">slides</a> (pptx)] 
		[<a href="./public/lecs/lec15-2pl-occ+notes.pptx">pptx+notes</a>] <br/>
		OCC validate conditions (Slide#68/69) [<a href="./public/lecs/occ_validate.pdf">notes</a>]
  </td>
  <td class="reading">
		
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Nov 8 -->
  <td id="2021-11-11" class="date"><b>Thu 11/11</b></td>
  <td class="advanced">
		<b>Lec 16:</b> Spark
		[<a href="./public/lecs/lec16-spark.pdf">slides</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/spark_nsdi12.pdf">Spark</a> paper (optional)
  </td>
  <td class="hwdue">
		<a href="./lab2.html">Lab 2c</a> due
		<p style="color:black">Lab 3 out</p>
  </td>
</tr>

<tr> <!-- week of Nov 15 -->
  <td id="2021-11-16" class="date"><b>Tue 11/16</b></td>
  <td class="advanced">
		<b>Lec 16-2:</b> Spark
		[<a href="./public/lecs/lec16-spark.pdf">slides</a>]
  </td>
  <td class="reading">
		<a href="./public/papers/spark_nsdi12.pdf">Spark</a> paper (optional)
  </td>
  <td class="nodue">
  </td>
</tr>
<tr> <!-- week of Nov 15 -->
  <td id="2021-11-18" class="date"><b>Thu 11/18</b></td>
  <td class="advanced">
		<b>Lec 17:</b> Facebook (Meta?) Memcache
  </td>
  <td class="reading">
		<a href="./public/papers/memcache_nsdi13.pdf">Scaling memcache</a> paper (optional)
  </td>
  <td class="nodue">
  </td>
</tr>

<tr> <!-- week of Nov 22 -->
  <td id="2021-11-23" class="date"><b>Tue 11/23</b></td>
  <td class="advanced">
		<b>Lec 18:</b> Cloud computing and serverless computing
  </td>
  <td class="reading">
		
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
