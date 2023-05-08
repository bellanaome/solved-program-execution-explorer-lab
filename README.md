Download Link: https://assignmentchef.com/product/solved-program-execution-explorer-lab
<br>
Useful pointers

<ul>

 <li><a href="https://sourceware.org/gdb/current/onlinedocs/gdb/">Debugging with GDB</a> (2015)</li>

 <li><a href="https://www.gnu.org/software/emacs/manual/html_node/elisp/Arithmetic-Operations.html">Arithmetic operations</a> in the GNU Elisp manual (2015)</li>

</ul>

Background

The name of this assignment comes from the idea that a debugger like GDB is better thought of as a way of exploring program execution histories than merely as a debugger.

Although the GNU Emacs text editor is not intended for high performance numeric computation, its scripting language <a href="https://www.gnu.org/software/emacs/manual/html_node/elisp/index.html">Elisp</a> is reasonably widely used and Elisp applications needs adequate (if not stellar) performance for numeric applications. One such application, <a href="https://www.gnu.org/software/emacs/manual/html_node/calc/index.html">GNU Calc</a>, is a desk calculator that does a lot of arithmetic internally. Your goal is to see how much overhead is imposed by Emacs when doing standard arithmetic operations, in particular multiplication with two integer arguments, and to think about how to reduce the arithmetic overhead.

Keep a log

Keep a log in the file <samp>pexexlab.txt</samp> of what you do in the lab so that you can reproduce the results later. This should not merely be a transcript of what you typed: it should be more like a lab notebook, in which you briefly note down what you did and what happened. It should record not just what worked, but also what <em>didn’t</em> work.

Gather instruction traces

You can multiply two numbers with Emacs from the shell by running a command like this:

<pre><samp>  emacs -batch -eval '(print (* 37 -26))'</samp></pre>

Gather a trace for the key part of the above test case. This trace should include every instruction in the <samp>Ftimes</samp> function, which is the C implementation of the Elisp <samp>*</samp> function. It should also include every instruction in every function that <samp>Ftimes</samp> calls, either directly or indirectly.

For the purpose of this assignment, a trace is an ASCII text file. Each line corresponds to a single machine instruction executed in turn during the trace. Lines use the following format:

<pre><samp>0x8120921&lt;arith_driver+1&gt;data.c:2577	push %edi	M[0xffffc9c4]=0x084073c2 esp=0xffffc9c4</samp></pre>

Columns should be separated by single tab characters. The first column gives the machine address of the instruction, both in hexadecimal and (in angle brackets) as an offset from the current function); this address is followed by the <a href="https://en.wikipedia.org/wiki/Basename">basename</a> of the source file and line number that is most responsible for the instruction; the example source line is the <samp>{</samp> at the start of the <samp>arith_driver</samp> procedure, since the instruction is part of that function’s <a href="https://en.wikipedia.org/wiki/Function_prologue">prolog</a>. The second column gives the machine instruction in the same format used by GDB’s <samp>x/i</samp> command, using a space to separate the instruction from operands. The third column gives the effect of the instruction on memory and general-purpose registers, again using hexadecimal values. The example above stores 0x084073c2 into the memory word at address 0xffffc9c4; the leading 0 in “084073c2” reminds the reader that it’s a 32-bit operation. The example also sets the <samp>esp</samp> register to 0xffffc9c4. List memory modifications in increasing address order, and register modifications in alphabetical order. Traces need not record modifications to status registers such as <samp>eflags</samp>.

To gather information for your first trace (which you should put into the file <samp>trace1.tr</samp>), use the executable <samp>~eggert/bin32/bin/emacs-24.5</samp> on the SEASnet GNU/Linux servers. The corresponding source code can be found in <samp>~eggert/src/emacs-24.5/</samp> (particularly its <samp>src</samp> subdirctory), and the executable was compiled for the x86. The above example trace line corresponds to line 2577 of <samp>~eggert/src/emacs-24.5/src/data.c</samp>.

Gather a second trace <samp>trace2.tr</samp> for the same test case from the executable <samp>~eggert/bin64/bin/emacs-24.5</samp> on the same platform. It is generated from the same source code, and was compiled for the x86-64.

Gather a third trace <samp>trace3.tr</samp> from a test case that prints <samp>(* most-positive-fixnum most-positive-fixnum)</samp> instead. Use the x86 Emacs for this test case; on this platform, <samp>most-positive-fixnum</samp> is 536870911.

Examine integer overflow handling

Compile the following function:

<pre><samp>  #include &lt;limits.h&gt;  int big = INT_MAX;  int  testovf (void)  {    return big + 1 &lt; big;  }</samp></pre>

for the x86 in three ways: (1) with <samp>-O2</samp>, (2) with <samp>-O2 -ftrapv</samp>, (3) with <samp>-O2 -fwrapv</samp>. Compare the resulting assembly-language files, and describe and justify the differences that you see. Put your description into a plain ASCII text file <samp>testovf.txt</samp>.

A few more questions

Answer the following questions, in a plain text file <samp>answers.txt</samp>:

<ol>

 <li>Explain why the instructions in the third trace did not produce the correct mathematical result. Which instructions caused the problem, exactly?</li>

 <li>If you just count instructions, which trace is the most efficient and why?</li>

 <li>Similarly, which is the least efficient and why?</li>

 <li>Where did the number 536870911 come from? Explain in terms of the Emacs source code.</li>

 <li>The two Emacs executables were compiled with GCC’s <samp>-O2</samp> option. Suppose they had also been compiled with <samp>-ftrapv</samp>. Explain any problems the traces would run into, or if there would not be a problem explain why not.</li>

 <li>Similarly, discuss whether and how <samp>-fwrapv</samp> would have caused problems.</li>

 <li>Suppose we assume <samp>-fwrapv</samp> is used. Suggest changes to how Emacs does integer multiplication that should help improve its performance. Focus on just the integer multiplication; don’t alter the machinery Emacs uses to decide which flavor of multiplication to do.</li>

 <li>How significant are the efficiency differences discussed above, in the context of Emacs execution?</li>

</ol>

Submit

Submit a compressed tarball <samp>pexex.tgz</samp> containing the files mentioned above, namely <samp>pexexlab.txt</samp>, <samp>trace1.tr</samp>, <samp>trace2.tr</samp>, <samp>trace3.tr</samp>, <samp>testovf.txt</samp>, and <samp>answers.txt</samp>.


