Download Link: https://assignmentchef.com/product/solved-csci-2500-pipeline-and-cache-iplc-simulator
<br>
<h1>1     Overview</h1>

You will be implementing and comparing various implementations of a combined instruction pipeline and cache (IPLC) simulator. In particular, you will be determining what design yields the best overall performance (i.e., fewest cycles). For the pipeline/cache simulation part, you will be given a template which you’ll need to extend, that contains the following set of files.

<ul>

 <li><strong>instruction-trace.txt: </strong>This is an instruction trace file. The format is described below.</li>

 <li><strong>iplc-sim.c: </strong>template for the simulator code.</li>

 <li><strong>Makefile: </strong>builds the iplc simulator.</li>

 <li><strong>taken-2-2-2.out: </strong>trace-file output for the case described below. Used to help you debug your implementation.</li>

</ul>

<h2>1.1           Instruction Trace Format</h2>

Figure 1, provides a example of the nearly 35,000 plus MIPS instruction trace. This trace is from a 2-D Matrix Swap and Multiple MIPS code.

The format for each instruction is as follows. First, each instruction occupies a single line of text. The first field is the memory address at which the instruction is located. Next, follows the MIPS instruction mnemonic (e.g., lw for Load Word followed by the registers and or offset that might be used as part of the instruction. A “colon” is used to denote the instruction has ended and the address that follows is the absolute memory DATA address that was read or written by that 0x00400000 lw $4, 0($29): 7fffef48

0x00400004 addiu $5, $29, 4

0x00400008 addiu $6, $5, 4 0x0040000c sll $2, $4, 2

0x00400010 addu $6, $6, $2

0x00400014 jal 0x00400024

0x00400024 addi $29, $29, -4

0x00400028 sw $31, 0($29): 7fffef44

0x0040002c lui $4, 4097

0x00400030 lui $1, 4097

0x00400034 ori $5, $1, 200

0x00400038 ori $6, $0, 5

0x0040003c jal 0x0040025c 0x0040025c ori $8, $0, 4

0x00400260 add $9, $0, $0

0x00400264 beq $9, $8, 180

0x00400268 ori $10, $0, 2

0x0040026c beq $10, $8, 164

0x00400270 add $11, $9, $9

0x00400274 add $11, $11, $11

0x00400278 add $11, $11, $11

0x0040027c add $11, $11, $11

0x00400280 add $12, $10, $10

0x00400284 add $12, $12, $12

0x00400288 add $11, $11, $12

0x0040028c add $11, $11, $4

0x00400290 lw $13, 0($11): 10010008

Figure 1: Example instruction trace from the MIPS 2-D Matrix Swap and Multiple Program. There are 34,753 instructions in this trace!

instruction if it accessed memory. Instructions that do not access memory will not have a “colon” nor DATA address following.

<strong>NOTE: I have provided you with the code necessary to parse this instruction trace file</strong>.

<h2>1.2           Cache Details</h2>

Using this cache simulator and INSTRUCTION and DATA address trace (which the cache simulator skeleton code reads in for you), your overall goal is to find the optimal values for (1) block size, and (2) associativity such that the miss rate of the cache is minimal, yet the total size of the cache must <strong>be less than 10240 bits</strong>. Notice the total size of the cache is expressed in “bits”, NOT “bytes”!!). Possible cache configurations are the following:

<ul>

 <li><strong>Possible Block Sizes:</strong>

  <ul>

   <li>1 word (32 bits)</li>

   <li>2 words (64 bits)</li>

   <li>4 words (128 bits)</li>

  </ul></li>

 <li><strong>Levels of Associativity</strong>

  <ul>

   <li>direct mapped (1-way set associative)</li>

   <li>2-way set associative</li>

   <li>4-way set associative</li>

  </ul></li>

</ul>

<strong>Cache Size Function: </strong>You can express the size of the cache in terms of 3 factors:

<ul>

 <li>BlockSize</li>

 <li>Associativity</li>

 <li>IndexBits</li>

</ul>

<em>CacheSize </em>= <em>Associativity</em>∗(2<em><sup>IndexBits</sup></em>∗(32∗<em>BlockSize</em>+33−<em>IndexBits</em>−<em>BlockOffSetBits</em>))

where <em>BlockOffSetBits </em>is the <em>Log</em><sub>2 </sub>of the BlockSize, which can be computed as follows: <em>BlockOffSetBits </em>= <em>NaturalLog</em>(4 ∗ <em>BlockSize</em>)<em>/NaturalLog</em>(2)

Using the above equation (which has been coded for you in the cache skeleton), you need to find the largest value for the index such that it results in a total cache size that is less than or equal to maximum cache size of 10,240 (10k) bits FOR EACH combination of blocks size and level of associativity. Thus, there will be 9 value combinations of index size, block size and level of associativity. Your cache simulator should compute this.

Use your software skills to build a “cache” data structure that is dynamic (i.e., use malloc()). Meaning, I do not want to see 9 hard coded cache data structures that correspond to the above 9 different configurations.

You are to assume that on reads and writes, the data is brought into the cache – i.e., to simplify things, you will have a “write/miss” in this cache.

In order for you to check yourself, I have provide the pipeline trace and cache performance output for the 2 bits of cache index, 2 word block size, 2-way set associative, branches taken case. Note, this case is not the largest cache size you could have had and is solely to help you debug.

As you will see, for this very small cache (only 2480 bits) the cache miss rate is high (i.e., <em>&gt; </em>16%). You are allowed to have a MAX cache size of 10240 bits. So, you need to determine the 9 possible configurations using the template such that each configuration is the maximum index (number of cache lines) for a given block size and level of associativity. Equally, if your cache simulator does not get this precise answer, then your cache simulator is incorrect and you will not get the performance in cycles correct.

<h2>1.3           Pipeline Details</h2>

The pipeline is the standard 5-stage design consisting of FETCH, DECODE, ALU, MEM and WRITEBACK stages. The design features you are to consider are:

<ul>

 <li>On a cache instruction MISS, the pipeline FETCH stage incurs a <strong>stall penalty </strong>of 10 clock cycles.</li>

 <li>On a data word MISS (LW/SW instruction) is also a 10 clock cycle <strong>stall penalty </strong>in the MEM stage for LW and WRITEBACK stage for SW.</li>

 <li>Determine which branch prediction method yields better performance, TAKEN or NOTTAKEN?</li>

 <li>Last, you are to “forward” registers across stages to avoid pipeline stalls.</li>

</ul>

Note, you will know if a branch is taken because you can see if the address for the next instruction is 4 bytes larger or not. That is, if <em>PC </em>+4 was not the next instruction, then you know the branch was TAKEN!!. Thus, if your predictor guesses wrong, you just need to insert the NOP instruction into the pipeline but do not count that as a real instruction but count the cycles.

The forwarding logic can be found in the textbook. Note, however, your logic is much easier. You will know what instruction you are processing as opposed to particular control signal lines. For example, if either Reg1 or Reg2 being used in the DECODE stage are the Dest Reg in the ALU, or MEM stages, you can FORWARD either Reg1 or Reg2 to avoid the stall. That is when you detect this condition, you’ll allow the pipeline to continue without stalling (e.g., adding delay cycles to overall cycle count). Also note that if a Dest Reg in the WRITEBACK stage is used in the DECODE stage, then no forwarding is needed since the write occurs in the first half of the clock cycle and the read occurs in the last half of the clock cycle for that pipeline stage.

The functions you will write for this functionality are detailed in comments provided in the template code iplc-sim.c which I will cover in class.

<h1>2             Performance Evaluation</h1>

You will run your simulator 18 times – 9 cache configurations plus branch predictor configured TAKEN and 9 cache configurations plus branch predictor configured NOT-TAKEN.

<strong>Show the summary output for all your runs and indicate which configuration performed best for this instruction trace file.</strong>

<h1></h1>