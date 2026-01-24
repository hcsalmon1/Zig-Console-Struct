# Zig-Console-Struct

Reading and writing from the console in Zig can be quite complicated:

New way:
```zig
	var write_buffer:[1024]u8 = undefined;
	var read_buffer:[1024]u8 = undefined;
	var console:Console = undefined;
	console.init(&write_buffer, &read_buffer);

	try console.println("Hello World!", .{}); //will flush for you
```
Old way:
```zig
    var write_buffer:[1024]u8 = undefined;
    var stdout_writer = std.fs.File.stdout().writer(&write_buffer);
    const writer:*std.Io.Writer = &stdout_writer.interface;

    try writer.print("Hello World!", .{}); //print only writes to the buffer
    try writer.flush(); //flush will actually print the text to the console
    
    var read_buffer:[1024]u8 = undefined;
    var stdin_reader = std.fs.File.stdin().reader(read_buffer);
    var reader = &stdin_reader.interface;

    try reader.
    var line:[]u8 = try self.reader.takeDelimiterExclusive('\n');

    //remove \r if windows
    if (line.len > 0 and line[line.len - 1] == '\r') {
      line = line[0 .. line.len - 1];
    }

    try writer.print("input: {s}\n", .{line});
    try writer.flush();
```

This is hard to remember, tedious and long to write each time. I wanted to make this simpler.
Solution:


This does everything for you. It gets stdin and stdout, gets the reader and writer and then references the interface.

'console' has to be set to undefined at first because reader and writer are pointers and would be dangling pointers if 
created with an init function immediately. I wanted to avoid heap allocation where necessary.

Usage:

```zig
fn consoleInput(console:*Console) !void {

    while (true) {

        try console.println("Write something:", .{});
        const line:[]u8 = try console.readLine();
        if (std.mem.eql(u8, line, "close") == true) {
            break;
        }

        try console.println("You typed: '{s}'", .{line});
        
    }
}
```
Reading and writing is as simple as this. You just pass the Console to functions by reference.
 
Console has many useful functions but not all of them. Such as:
println - writes to the buffer and flushes to the console with a new line
print - writes to the buffer and flushes to the console
writeln - writes to the buffer with a new line
write - writes to the buffer
flush - prints to the console and clears the buffer
readline - reads until '\n', removes '\r' for windows, returns ![]u8
fill - fills the read buffer for n number of bytes
peek - look at the next n number of bytes, return ![]u8
peekByte - returns the next byte without incrementing index, !u8
readByte - gets and returns the next byte and increments the index, !u8

For all the other functions, use this:

```zig

	console.reader.toss(10);
	console.writer.undo(10);

```
