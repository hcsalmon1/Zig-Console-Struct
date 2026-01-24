# Zig-Console-Struct

Reading and writing from the console in Zig can be quite complicated:

```zig
    var write_buffer:[1024]u8 = undefined;
    var stdout_writer = std.fs.File.stdout().writer(&write_buffer);
    const writer:*std.Io.Writer = &stdout_writer.interface;

    try writer.print("", .{}); //print only writes to the buffer
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
```zig
	var write_buffer:[1024]u8 = undefined;
	var read_buffer:[1024]u8 = undefined;
	var console:Console = undefined;
	console.init(&write_buffer, &read_buffer);
```

This does everything for you. It gets 

'console' has to be set to undefined at first because reader and writer are pointers and would be dangling pointers if 
created with an init function immediately. I wanted to avoid heap allocation where necessary.

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


    
