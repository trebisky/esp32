Here are some tools I use to disassemble the ESP32 bootrom

In particular, espdis is a ruby program that is a frontend
for objdump that I use to generate a much more civilized
listing than naive use of objdump will produce.

startup.dis -- only disassemble what the startup can reach.
