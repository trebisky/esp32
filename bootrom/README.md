Here are some tools I use to disassemble the ESP32 bootrom

In particular, espdis is a ruby program that is a frontend
for objdump that I use to generate a much more civilized
listing than naive use of objdump will produce.

Also you will find the current disassembly output here.
There are two listings.  

startup.dis -- 7500 lines - only code reached by startup reset
everything.dis -- 112,000 lines - what can be reached from
    every entry point I am aware of at this time.
