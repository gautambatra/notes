# File I/O

## C-style
* Defined in <cstdio>
* `std::FILE`: C stream associated with an external device like file, stdin, printer, serial port, etc. 

* `std::ftell( std::FILE* stream )`
    * Returns current value of position indicator for the given FILE stream
    * In binary mode, returns number of bytes from beginning
    * In text mode, value is only meaningful as input to `std::fseek`

* `std::fseek ( std::FILE* stream, long offset, int origin )`
    * set file position indicator for an std::FILE stream relative to the origin
    * origin can be `SEEK_SET` (beginning), `SEEK_CUR` (current position), `SEEK_END` (end)
    * For binary streams, SEEK_END is not required to be supported
    * For text streams, offset can only be 0 (works with any origin), or a value returned by `std::ftell` with SEEK_CUR as origin

* `std::rewind`
    * Move to beginning of file
    * equivalent `std::fseek(stream, 0, SEEK_SET)`, except rewind clears error and end-of-file flags, and doesn't return int

* `fgetpos`, `fsetpos`
    * Provide portability to non unix/posix like systems which may have record-style files instead of byte streams
    * Use the fpos_t object instead of integer offsets
    * `std::fpos_t`: Object type used for specifying position and state of a FILE stream
    * `int fgetpos( std::FILE* stream, std::fpos_t* pos )`
        * Obtain the current position of `stream` and store it in `pos`
    * `int fsetpos( std::FILE* stream, const std::fpos_t* pos )`
        * Set the position of `stream` to `pos`, which is obtained from from a `fgetpos` call on a stream associated with the same file

* `std::FILE* fopen( const char* filename, const char* mode )`
    * "b" in mode opens the file in binary mode, but this doesn't have any effect in posix systems. On windows, disables handling of '\n'

* `std::size_t fread( void* buffer, std::size_t size, std::size_t count, std::FILE* stream )`
    * size is size of each object, count is number of object
    * returns number of objects successfully reaad, which may be less than count

```cpp
// Reading a file into a string
std::FILE *fp = std::fopen(filename, "rb");
if (fp)
{
    std::string contents;
    std::fseek(fp, 0, SEEK_END);
    contents.resize(std::ftell(fp));
    std::rewind(fp);
    std::fread(&contents[0], sizeof(contents[0]), contents.size(), fp);
    std::fclose(fp);
}
```


## C++ Style
### Opening a binary file for reading
```cpp
// Method 1
std::ifstream ifs {"filename.dat", std::ios::in | std::ios::binary};
if (!ifs.is_open())
{
    std::cerr << "Unable to open file\n";
    exit(1)
}

// Method 2
std::ifstream ifs;
ifs.open("filename.dat", std::ios::in | std::ios::binary);
if (!ifs.is_open())
{
    std::cerr << "Unable to open file\n";
    exit(1)
}
```


### Reading data from a binary file
```cpp
std::ifstream ifs {fileName, std::ios::in | std::ios::binary};
const auto fileSize = std::filesystem::file_size (fileName);
char buffer[fileSize];

// read fileSize bytes into buffer
ifs.read(buffer,fileSize);

// read an integer
std::uint_32t num;
ifs.read(reinterpret_cast<char*>(&num), sizeof(num));

// reading into a string
ifs.seekg(0);
std::string str(fileSize,'\0');
ifs.read(&str[0], fileSize);
```


### Writing data to binary file
```cpp
double d = 3.14;
std::ofstream ofs ("filename.dat", std::ios::out | std::ios::binary);
ofs.write(reinterpret_cast<char*>(&d), sizeof(d));
```

## References
* [cppreference - C-style file input/output](https://en.cppreference.com/w/cpp/io/c)
* [Stackexchange: Why does the C library include fgetpos()/fsetpos() if the same functionality can be achieved with ftell()/fseek()?](https://langdev.stackexchange.com/questions/2895/why-does-the-c-library-include-fgetpos-fsetpos-if-the-same-functionality-can)
* [Insane coding blog - How to read in a file in C++ ](https://insanecoding.blogspot.com/2011/11/how-to-read-in-file-in-c.html)
