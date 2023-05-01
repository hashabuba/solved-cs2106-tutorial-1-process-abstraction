Download Link: https://assignmentchef.com/product/solved-cs2106-tutorial-1-process-abstraction
<br>
[Function invocation – the gory details] Let’s use a “simple” function to really understand the idea of stack frames and calling conventions. Note that the stack frame layout in this question is slightly different from the one covered in lecture 2. So, ensure you have a good understanding of the basics before attempting this question.




Given below is an <strong>iterative </strong>factorial function in C.




<table width="528">

 <tbody>

  <tr>

   <td width="528">C</td>

  </tr>

  <tr>

   <td width="528"><strong>int <em>iFact</em>( int N ) </strong><strong>{    </strong><strong>     int result = 1, i; </strong><strong> </strong><strong>     for (i = 2; i &lt;= N; i++){          result = result * i; </strong><strong>     }  </strong><strong>     return result; </strong><strong>}</strong></td>

  </tr>

 </tbody>

</table>




<ol start="5">

 <li>[Code translation] Take a look at the partial assembly code translation on pages 4 &amp; 5. You should find most of it (vaguely) familiar from your basic assembly programming course. The remaining missing pieces are all related to the function call (setup/tear down of stack frame).</li>

</ol>




Suppose the following stack frame is used on this platform. For simplicity, all integers and registers are assumed to occupy 4 bytes, and the stack region is “growing” towards lower address.







<table width="284">

 <tbody>

  <tr>

   <td colspan="3" width="284">Unused Stack Memory Space</td>

  </tr>

  <tr>

   <td rowspan="2" width="130">Local Variable</td>

   <td width="49">-36</td>

   <td width="104">[result]</td>

  </tr>

  <tr>

   <td width="49">-32</td>

   <td width="104">[i]</td>

  </tr>

  <tr>

   <td width="130">Parameter</td>

   <td width="49">-28</td>

   <td width="104">[N]</td>

  </tr>

  <tr>

   <td width="130"><strong>Return Result </strong></td>

   <td width="49">-24</td>

   <td width="104"><strong> </strong></td>

  </tr>

  <tr>

   <td rowspan="3" width="130"><strong>Saved Registers </strong></td>

   <td width="49">-20</td>

   <td width="104">[$11]</td>

  </tr>

  <tr>

   <td width="49">-16</td>

   <td width="104">[$12]</td>

  </tr>

  <tr>

   <td width="49">-12</td>

   <td width="104">[$13]</td>

  </tr>

  <tr>

   <td width="130">Saved SP</td>

   <td width="49">-8</td>

   <td width="104"></td>

  </tr>

  <tr>

   <td width="130">Saved FP</td>

   <td width="49">-4</td>

   <td width="104"></td>

  </tr>

  <tr>

   <td width="130">Saved PC</td>

   <td width="49"> 0</td>

   <td width="104"></td>

  </tr>

 </tbody>

</table>

— Stack Pointer ($<strong>sp</strong>)










<strong> </strong>







— Frame Pointer ($<strong>fp</strong>)













Complete the memory offsets in the <strong>lw</strong>/<strong>sw</strong> instructions (they are tagged with “Part a”).  You can assume that the <strong>$sp</strong> and <strong>$fp</strong> registers are initialized properly.




<ol start="2">

 <li>[Stack frame – caller prepares to call a function] Refer to the calling convention sample given in lecture 2. Assume we make the following function call <strong><em>iterativeFactorial</em></strong><strong>( 10 ) </strong>from the <strong><em>main</em></strong><strong>()</strong></li>

</ol>

<strong> </strong>

Essentially, you need to:

<ul>

 <li>Pass the parameter (“10”) onto the stack.</li>

 <li>Save the PC to return to on the stack. For simplicity, we assume there is a “call <strong><em>function offset(register)</em></strong><strong>“</strong> This instruction <strong>saves the</strong> <strong>next PC</strong> to the memory address specified by “offset(register)”, then jumps to the specified “function”.</li>

</ul>

Complete the relevant portions tagged with “Part b”.




<ol>

 <li>[Stack frame – callee enters function] Now, let us fill in the instructions for the callee to setup the stack frame upon entering the function. Tasks required:

  <ul>

   <li>Save the registers used in the caller (i.e. $11-$13).</li>

   <li>Save the current FP, SP registers on the stack.</li>

   <li>Allocate space for local variables, i.e. “Result”, “i”.</li>

   <li>Adjust the special registers FP, SP.</li>

  </ul></li>

</ol>




<ol>

 <li>[Stack frame – callee exits function] At the end of the function, we need to:

  <ul>

   <li>Place the return result onto stack frame.</li>

   <li>Restores the saved registers, FP, and SP.</li>

   <li>Return to the caller with the saved PC. For simplicity, we assume there is a “<strong>return offset(register)</strong>” instruction, which overwrites the PC with the value stored at the memory location “<strong>offset(register)</strong>“.</li>

  </ul></li>

</ol>




With (d), we now have a complete demonstration of the idea of stack frames, calling conventions and the usage of stack / frame pointers.

<strong> </strong>

<ol>

 <li>[Extra challenge – not discussed] Draft a solution for a <strong>recursive version of </strong> The bits and pieces from (a), (c) and (d) are very similar, the only tricky part is that the recursive factorial function is both a caller and a callee… So, figuring out where (b) should be placed is the main challenge.</li>

</ol>







Notes: To illustrate a generic calling convention, we have to provide several made-up instructions, e.g. <strong>call</strong> and <strong>return</strong>. Feel free to explore other real calling conventions on different platforms, e.g. Intel x86, MIPS, etc, for different languages, e.g. C/C++, Python and Java on JVM.  You should be able to see the same ideas echoed across different environments.




<table width="588">

 <tbody>

  <tr>

   <td width="588">MIPS-like Assembly Code</td>

  </tr>

  <tr>

   <td width="588"><strong>iFact: </strong><strong>      </strong><strong>#Part (c) – Callee enter function</strong><strong>      </strong><strong>                        </strong><strong>#save registers </strong><strong>       </strong><strong>                              </strong><strong>#save $fp, $sp</strong><strong>        </strong><strong>                              </strong><strong>#move $fp, $sp to</strong><strong>  </strong><strong>                              </strong><strong>#     new position</strong><strong>      </strong><strong>#Part (c) – Callee enter function ends</strong><strong> </strong><strong>      addi $11, $0, 1         </strong><strong>#init “result” </strong><strong>      sw   $11, </strong><strong>____</strong><strong>($fp)     </strong><strong>##Part (a) result = 1 </strong><strong> </strong><strong>      addi $12, $0, 2         </strong><strong>#init “i” </strong><strong>      sw   $12, </strong><strong>____</strong><strong>($fp)     </strong><strong>##Part (a) i = 2</strong><strong> </strong><strong>      lw   $13, </strong><strong>____</strong><strong>($fp)     </strong><strong>##Part (a) Get N </strong><strong>loop: bgt  $12, $13, end </strong><strong>       </strong><strong>      mul  $11, $11, $12      </strong><strong>#assume no overflow</strong><strong>       sw   $11, </strong><strong>___</strong><strong>($fp)      </strong><strong>##Part (a) update result </strong><strong> </strong><strong>      addi $12, $12, 1 </strong><strong>      sw   $12, </strong><strong>___</strong><strong>($fp)      </strong><strong>##Part (a) i++ </strong><strong>      j loop </strong><strong> end:   </strong><strong>      </strong><strong>#Part (d) – Callee exit function</strong><strong>       </strong><strong>      </strong><strong>                        </strong><strong>#save return result</strong><strong>  </strong><strong>                              </strong><strong>#restore registers</strong><strong>        </strong><strong> </strong><strong>                              </strong><strong>#restore $sp, $fp</strong><strong>        </strong><strong> </strong><strong>      return </strong><strong>        </strong><strong>#resume execution of the caller </strong>  <strong>       </strong><strong> </strong><strong> #Continues on the next page</strong> <strong> </strong><strong>### Main Function </strong></td>

  </tr>

 </tbody>

</table>

<strong>main: </strong>




……                <strong>#irrelevant code omitted </strong><strong> </strong>

<strong>#Part (b) – Caller prepare to call function </strong><strong>    addi  $13, $0, 10     </strong><strong>#Use $13 to store 10</strong><strong> sw  </strong><strong>                  </strong><strong>#Where should the “10” go?</strong>

<strong>    call iFact,        </strong><strong>#start executing the function </strong>

<strong> </strong>




<ol start="2">

 <li>[Functions and stack – AY1819S1 Midterm] In many programming languages, function parameter can be <strong>passed by reference</strong>. Consider this fictional C-like language example:</li>

</ol>

Mr. Holdabeer feels that he has the perfect solution <strong>that works for this example </strong>by relying on <strong>stack pointer and frame pointer. </strong>The key idea is to load main’s local variable “myInt” whenever the variable “i” is used in the change() function.

Given that the stack frame arrangement shown <strong>independently </strong>as follows:




<table width="542">

 <tbody>

  <tr>

   <td colspan="4" width="274">For Main()</td>

   <td colspan="4" width="268">For change()</td>

  </tr>

  <tr>

   <td rowspan="6" width="41"></td>

   <td colspan="2" width="130"></td>

   <td rowspan="6" width="103">ç <strong>$SP </strong>  ç <strong>$FP </strong></td>

   <td rowspan="6" width="38">  </td>

   <td colspan="2" width="130"></td>

   <td rowspan="6" width="100">ç <strong>$SP </strong>ç <strong>$FP </strong></td>

  </tr>

  <tr>

   <td width="84">…</td>

   <td width="45">…</td>

   <td width="85">Saved SP</td>

   <td width="45">-8</td>

  </tr>

  <tr>

   <td width="84"><strong>myInt </strong></td>

   <td width="45">-12</td>

   <td width="85">Saved FP</td>

   <td width="45">-4</td>

  </tr>

  <tr>

   <td width="84">Saved SP</td>

   <td width="45">-8</td>

   <td width="85">Saved PC</td>

   <td width="45">0</td>

  </tr>

  <tr>

   <td width="84">Saved FP</td>

   <td width="45">-4</td>

   <td colspan="2" rowspan="2" width="130"></td>

  </tr>

  <tr>

   <td width="84">Saved PC</td>

   <td width="45">0</td>

  </tr>

 </tbody>

</table>




<ol>

 <li>Suppose the main()’s and change()’s stack frame has been properly setup, and change() is now executing, show how to store the value “1234” into the right location. You only need pseudo-instructions like below. <strong>[3 marks]</strong></li>

</ol>

<strong> </strong>

<ul>

 <li>Register_D ß Load Offset( Register_S )</li>

</ul>

Load the value at memory location [Register_S] + Offset and put into

Register_D

e.g. $R1 ß Load -4($FP)

<ul>

 <li>Offset(Register_S) ß Store Value</li>

</ul>

Put the value into memory location [Register_S] + Offset, e.g. -4($FP) ß Store 1234




<ol>

 <li>Briefly describe another usage scenario for pass-by-reference parameter that <strong>will not work with </strong>this approach.</li>

</ol>

<strong> </strong>

<ol>

 <li>Briefly describe a better, universal approach to handle pass-by-reference parameter on stack frame. Sketch the stack frame for the change() function to illustrate your idea.</li>

</ol>




<ol start="3">

 <li>[Process state] Suppose we compile and execute the following C code. Using the 5-state process model, trace the corresponding process state transitions for the executable (“<strong>exe</strong>”) produced from the code below.</li>

</ol>




<table width="514">

 <tbody>

  <tr>

   <td width="514"><strong>int main() </strong><strong>{ </strong><strong>    int input, result; </strong><strong> </strong><strong>    </strong><strong>// will either cause a state transition?</strong><strong>    printf(“Give input below:
”);     scanf(“%d”, &amp;input); </strong><strong> </strong><strong>    </strong><strong>// takes a <u>long</u> time – math operations only </strong><strong>    result = ComplexFunc( input );  </strong><strong>      </strong><strong>    // write result to disk     </strong><strong>saveToDisk( result );</strong><strong>   printf(“Result is %d
”, result); </strong><strong> </strong><strong>  return 0; </strong><strong>}</strong></td>

  </tr>

 </tbody>

</table>




<strong> </strong>

<strong> </strong>

<strong> </strong>

<strong>Discussion question if time permits:  </strong>

<ol start="4">

 <li>[Process control block] Question 1 focuses on the use of <strong>stack memory </strong>within a single program. Let us take a step back and look at <strong>multiple executing programs. </strong>Suppose we execute the program containing the factorial function <strong>twice </strong>and both processes run in parallel (i.e. exist at the same time). Draw the PCBs of the two processes (similar to lecture 2’s slide on PCBs), indicate clearly what is laid out in the physical memory and how the different memory regions of a process fit together.</li>

</ol>

<strong> </strong>

<strong>Additional exercise question if time permits:  </strong>

<strong> </strong>

<ol start="5">

 <li>[MIPS and stack frames – a refresher] A snapshot of the current state of a program’s stack (along with the current locations of the stack and frame pointer) is given below. In this example, the second column refers to the difference in memory address from the Saved PC, and the third column refers to the value currently at that position. For simplicity, all integers and registers are assumed to occupy 4 bytes, and the stack region is “growing” towards lower addresses.</li>

</ol>




From this state, we execute a few MIPS-like instructions. Assume we have a function named <strong>print</strong> which prints the value in register <strong>$1.</strong> We can call it using: <strong>call print. What would be printed as a result of running each set of instructions?</strong> If there is no clear answer, state precisely why.




Also, recall that the second parameter of the <strong>lw</strong>/<strong>sw </strong>instruction has the form of <strong>offset(register)</strong>, e.g.  –<strong>12($fp)</strong> == (<strong>content of register</strong> <strong>$fp) – 12</strong>.




(Each sub-part of this question is independent, e.g.  b) behaves like a) never happened, and so on)




<table width="284">

 <tbody>

  <tr>

   <td colspan="3" width="284">Unused Stack Memory Space</td>

  </tr>

  <tr>

   <td rowspan="2" width="130">Local Variable</td>

   <td width="49">-36</td>

   <td width="104">22</td>

  </tr>

  <tr>

   <td width="49">-32</td>

   <td width="104">8</td>

  </tr>

  <tr>

   <td width="130">Parameter</td>

   <td width="49">-28</td>

   <td width="104">16</td>

  </tr>

  <tr>

   <td width="130"><strong>Return Result </strong></td>

   <td width="49">-24</td>

   <td width="104">7</td>

  </tr>

  <tr>

   <td rowspan="3" width="130"><strong>Saved Registers </strong></td>

   <td width="49">-20</td>

   <td width="104">9</td>

  </tr>

  <tr>

   <td width="49">-16</td>

   <td width="104">1</td>

  </tr>

  <tr>

   <td width="49">-12</td>

   <td width="104">3</td>

  </tr>

  <tr>

   <td width="130">Saved SP</td>

   <td width="49">-8</td>

   <td width="104"><em>not relevant </em></td>

  </tr>

  <tr>

   <td width="130">Saved FP</td>

   <td width="49">-4</td>

   <td width="104"><em>not relevant</em></td>

  </tr>

  <tr>

   <td width="130">Saved PC</td>

   <td width="49"> 0</td>

   <td width="104"><em>not relevant</em></td>

  </tr>

 </tbody>

</table>

— Stack Pointer ($<strong>sp</strong>)










<strong> </strong>







— Frame Pointer ($<strong>fp</strong>)













<ol>

 <li></li>

</ol>




<table width="588">

 <tbody>

  <tr>

   <td width="588">MIPS-like Assembly Code</td>

  </tr>

  <tr>

   <td width="588"><strong>      lw   $1, 0($sp)       call print </strong><strong> </strong></td>

  </tr>

 </tbody>

</table>




<ol>

 <li></li>

</ol>




<table width="588">

 <tbody>

  <tr>

   <td width="588">MIPS-like Assembly Code</td>

  </tr>

  <tr>

   <td width="588"><strong>      lw   $2, -16($fp) </strong><strong> </strong><strong>      add  $sp, $sp, $2 </strong><strong> </strong><strong>      lw   $1, 0($sp)       call print </strong><strong> </strong></td>

  </tr>

 </tbody>

</table>

<ol>

 <li></li>

</ol>




<table width="588">

 <tbody>

  <tr>

   <td width="588">MIPS-like Assembly Code</td>

  </tr>

  <tr>

   <td width="588"><strong>      lw   $sp, -16($fp) </strong><strong> </strong><strong>      lw   $1, 0($sp)       call print </strong><strong> </strong></td>

  </tr>

 </tbody>

</table>

<strong> </strong>