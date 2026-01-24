# Zig-Console-Struct

Reading and writing from the console in Zig can be quite complicated:

    var write_buffer:[1024]u8 = undefined;
    var stdout_writer = std.fs.File.stdout().writer(&write_buffer);
    const writer:*std.Io.Writer = &stdout_writer.interface;

    try writer.print("", .{});
    try writer.flush();
    
    var read_buffer:[1024]u8 = undefined;
    var stdin_reader = std.fs.File.stdin().reader(read_buffer);
    var reader = &stdin_reader.interface;

    try reader.
    var line:[]u8 = try self.reader.takeDelimiterExclusive('\n');

    //remove \r is windows
    if (line.len > 0 and line[line.len - 1] == '\r') {
      line = line[0 .. line.len - 1];
    }

    try writer.print("input: {s}\n", .{line});
    try writer.flush();


    
