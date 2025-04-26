# bfq - Bash f3c querier

This is a querier for the [Fine Format For Configuration](https://github.com/fmxsh/f3c) (_F3C_).

It is proof of concept implementation, but also meant to be fully useful.

Versions in other languages are planned.

## Usage

To illustrate the usage, consider this example file: myfile.f3c

```
fruit: apple
```

Call `bfq` like `bfq myfile.f3c fruit` or pipe the file: `cat myfile.f3c | bfq fruit`. Use the `-v` flag for verbose output, like `bfq -v ..."`.

Traverse a nested structure by adding keys as arguments like: `bfq myfile.f3c users bob mail`.

## Background

I set out to get down to specifyign my a configuration format by my own personal preference. I wrote this proof of concept parser and querier in Bash. Selecting such a rather limited language proves the simplicity of the configuration format. I developed the parser in paralell with working out the [_F3C_](https://github.com/fmxsh/f3c) parser.

> [!NOTE]
> The names of the functions aren't the best, but will be cleaned up later. _Linekey_ is the previous name of the parser.

`linekey_parser()` - Takes input data and the keys passed to it. Loops trough the keys. For the current key, calls `linekey_parser_wrapped()` to retrieve the litral (value) bound to the identifier (key). Now having the result associated with the key, then loops around and takes the next key, and takes the result from the previous iteration, and looks for the new key in that and returns its associated literal. Then loops around continiously till we reach the final key. Then the value associated with that final key is returned all the way back the recursive staircase and printed.

`linekey_parser_wrapped()` - Loops trough all lines given to it, check first if current line is a directive or comment and handles them accordingly. If not a directive or comment, calls `parser_parse_line()` on the line.

`parser_parse_line()` - The core state machine. Handles the single line sent to it, runs `parse_single_line()` on it, to determine the nature of the line, then goes on to interpret it in the context the previous lines it has parsed, like: is this line part of a multi-line literal, and if so, collect it as such.

`parse_single_line()` - A state machine. At the lowest level, parses character by character, to determine the syntax of the current line, and thus the type and possible data of the current line.

## Limitations

### Implicit identifiers

This implementation does not implement implicit identifiers, or rather, what strictly speaking are explicit implicit identifiers, like `.:` and `.::`.

I wrote the format _F3C_ and this parser to store and parse my configuration data. I have no use of array indexing. I never access a configuration by `bfq thefile.f3c fruits 1`, but rather `bfq thefile.f3c app api-key`.

## Todo

The `d()` for verbose debug output should be made its own command that `bfq` can call.
