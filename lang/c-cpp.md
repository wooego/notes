---
title: C & C++ Notes
...

C & C++ Notes
=============

>   Talk is cheap. Show me your achievements.

C++ 简介 | Intro

:   C++ (pronounced as cee plus plus, `/ˈsiː plʌs plʌs/`) is a general-purpose
    programming language. It has imperative, object-oriented and generic
    programming features, while also providing facilities for low-level memory
    manipulation.

    see more... -<

    :   可以重载的操作符：

        -----------------------------------------------------
        Operator                                Symbol
        -------------------------------         -------------
        Scope resolution operator               `::`

        Conditional operator                    `?:`

        dot operator                            `.`

        Member selection operator               `.*`

        "sizeof" operator                       `sizeof`

        "typeid" operator                       `typeid`
        -------------------------------         -------------

        一定要到 [cppreference.com](http://en.cppreference.com/w/) 多看看，
        上面有函数说明，有 demo 代码，还有 Possible implementation 什么的！
        简直了。

        refs and see also

        -   [C++ - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/C%2B%2B)

## C/C++ 拾遗 | Pearls

-   ambiguity: declaration or multiplication? -<

    :   ```cpp
        Class::X *p;
        // X can be object of Class, or a nested class.
        // so ambi'guity occurs
        Type::NestedType   * p;         // declare
        Type::object * p;               // multiplication
        ```

-   2d-vector is esier to use -<

    :   ```cpp
        // c
        int **ary = new int*[row_num];
        for(int i = 0; i < row_num; ++i) {
            ary[i] = new int[col_num];
        }
        // delete[] ary;

        // cpp
        vector<vector<int> > ary(row_num, vector<int>(col_num, 0));
        ```

-   使用 reserve 来避免不必要的重新分配 -<

    :   ```cpp
        // std::vector, Defined in header <algorithm>
        void std::vector::reserve( size_type new_cap );
        vector<int> nums;
        nums.reserve( 25 );
        ```

        refs and see also

        -   [std::vector::reserve - cppreference.com](http://en.cppreference.com/w/cpp/container/vector/reserve)

-   even *vs* odd，奇偶数判断 -<

    :   ```cpp
        // even
        x % 2
        // odd
        x % 2 != 0
        x & 0x1
        ```

-   equal? float/double 数的判等 -<

    :   ```cpp
        // for int
        a == b
        // for double
        fabs(a-b) < 1e-9        // math.h
        ```

-   文件读写，用 FILE，fscanf 或者 ifstream，getline -<

    :   ```cpp
        FILE *fp = fopen(filename, "r");
        while( 2 == fscanf( fp, "%d %s", &index, buf ) ) {
            // ...
        }
        fclose(fp);

        size_t fread(        void *ptr, size_t size, size_t nmemb, FILE *stream );
        size_t fwrite( const void *ptr, size_t size, size_t nmemb, FILE *stream );
        ```

        ```cpp
        std::ifstream file( filename.c_str(), ifstream::in );
        // std::ifstream file; file.open( filename.c_str(), ifstream::in );
        //
        if ( !file.is_open() ) { exit(-1); }

        string line;
        while ( getline(file, line) ) {
            // ...
        }
        ```

        ```cpp
        ofstream myfile;
        myfile.open ("example.txt");
        myfile << "Writing this to a file.\n";
        myfile.close();
        ```

        -----------     -------------------------------------------------------
        ios::in         Open for input operations.

        ios::out        Open for output operations.

        ios::binary     Open in binary mode.

        ios::ate        Set the initial position at the end of the file.
                        If this flag is not set, the initial position is the
                        beginning of the file.

        ios::app        All output operations are performed at the end of the
                        file, appending the content to the current content of
                        the file.

        ios::trunc      If the file is opened for output operations and it
                        already existed, its previous content is deleted and
                        replaced by the new one.
        -----------     -------------------------------------------------------

        --------------------------------------
        class           default mode parameter
        ---------       ----------------------
        ofstream        ios::out

        ifstream        ios::in

        fstream         ios::in | ios::out
        ---------       ----------------------

        The following member functions exist to check for specific states of a
        stream (all of them return a bool value):

        -   `bad()` -<
              ~ Returns true if a reading or writing operation fails. For
                example, in the case that we try to write to a file that is not
                open for writing or if the device where we try to write has no
                space left.
        -   `fail()` -<
              ~ Returns true in the same cases as bad(), but also in the case
                that a format error happens, like when an alphabetical
                character is extracted when we are trying to read an integer
                number.
        -   `eof()` -<
              ~ Returns true if a file open for reading has reached the end.
        -   `good()` -<
              ~ It is the most generic state flag: it returns false in the same
                cases in which calling any of the previous functions would
                return true. Note that good and bad are not exact opposites
                (good checks more state flags at once).

        获取文件字节数

        ```cpp
        // C version
        FILE *ifp = fopen( "example.bin", "rb" );
        fseek( ifp, 0, SEEK_END );
        size_t len = ftell( ifp );
        fseek( ifp, 0, SEEK_SET );
        fclose( ifp );
        printf( "size is: %d bytes.\n", len );

        // C++ version
        streampos begin,end;
        ifstream ifs( "example.bin", ios::binary );
        begin = ifs.tellg();
        ifs.seekg( 0, ios::end );
        end   = ifs.tellg();
        ifs.close();
        cout << "size is: " << (end-begin) << " bytes.\n";
        ```

        -   get/read: `seekg ( position );`, `seekg ( offset, direction );`, `tellg()`
        -   put/write: `seekp ( position );`, `seekp ( offset, direction );`, `tellp()`

        ----------------------          -----------------------------------------------
        `ios::beg`, `SEEK_SET`          offset counted from the beginning of the stream

        `ios::cur`, `SEEK_CUR`          offset counted from the current position

        `ios::end`, `SEEK_END`          offset counted from the end of the stream
        ----------------------          -----------------------------------------------

        reading an entire binary file

        ```cpp
        #include <iostream>
        #include <fstream>
        using namespace std;

        int main () {
            streampos size;
            char * memblock;

            ifstream file( "example.bin", ios::in|ios::binary|ios::ate );
            if ( file.is_open() ) {
                size = file.tellg();
                memblock = new char[size];
                file.seekg( 0, ios::beg );
                file.read( memblock, size );
                file.close();

                cout << "the entire file content is in memory";
                delete[] memblock;
            } else { cout << "Unable to open file"; }
        }
        ```

        refs and see also

        -   [raw2pts/raw2pts.c at master · district10/raw2pts](https://github.com/district10/raw2pts/blob/master/raw2pts.c)
        -   [Input/output with files - C++ Tutorials](http://www.cplusplus.com/doc/tutorial/files/)

-   atoi, strtol, strtof, etc -<

    :   -   atoi, atol, atoll -<

            :   ```cpp
                int       atoi(  const char *str );
                long      atol(  const char *str );
                long long atoll( const char *str );

                printf( "%i\n", atoi(" -123junk") ); // -123
                ```

        -   strtol, strtoll, strtoul, strtoull -<

            :   ```cpp
                long      strtol(  const char          *str, char          **str_end, int base );
                long      strtol(  const char *restrict str, char **restrict str_end, int base );
                long long strtoll( const char *restrict str, char **restrict str_end, int base );
                ```

                ```cpp
                #include <stdio.h>
                #include <errno.h>
                #include <stdlib.h>

                int main(void)
                {
                    const char *p = "10 200000000000000000000000000000 30 -40";
                    printf("Parsing '%s':\n", p);
                    char *end;
                    for (long i = strtol(p, &end, 10);
                         p != end;
                         i = strtol(p, &end, 10))
                    {
                        printf("'%.*s' -> ", (int)(end-p), p);
                        p = end;
                        if (errno == ERANGE){
                            printf("range error, got ");
                            errno = 0;
                        }
                        printf("%ld\n", i);
                    }
                }
                ```

                ```cpp
                unsigned long      strtoul(  const char          *str, char          **str_end, int base );
                unsigned long      strtoul(  const char *restrict str, char **restrict str_end, int base );
                unsigned long long strtoull( const char *restrict str, char **restrict str_end, int base );
                ```

                refs and see also

                -   [strtol, strtoll - cppreference.com](http://en.cppreference.com/w/c/string/byte/strtol)
                -   [strtoul, strtoull - cppreference.com](http://en.cppreference.com/w/c/string/byte/strtoul)

        -   strtof, strtod, strtold -<

            :   ```cpp
                float       strtof(  const char *restrict str, char **restrict str_end );
                double      strtod(  const char *         str, char **         str_end );
                double      strtod(  const char *restrict str, char **restrict str_end );
                long double strtold( const char *restrict str, char **restrict str_end );
                ```

                ```cpp
                #include <stdio.h>
                #include <errno.h>
                #include <stdlib.h>

                int main(void)
                {
                    const char *p = "111.11 -2.22 0X1.BC70A3D70A3D7P+6  1.18973e+4932zzz";
                    printf("Parsing '%s':\n", p);
                    char *end;
                    for (double f = strtod(p, &end); p != end; f = strtod(p, &end))
                    {
                        printf("'%.*s' -> ", (int)(end-p), p);
                        p = end;
                        if (errno == ERANGE){
                            printf("range error, got ");
                            errno = 0;
                        }
                        printf("%f\n", f);
                    }
                }
                //          Parsing '111.11 -2.22 0X1.BC70A3D70A3D7P+6  1.18973e+4932zzz':
                //          '111.11' -> 111.110000
                //          ' -2.22' -> -2.220000
                //          ' 0X1.BC70A3D70A3D7P+6' -> 111.110000
                //          '  1.18973e+4932' -> range error, got inf
                ```

                refs and see also

                -   [strtof, strtod, strtold - cppreference.com](http://en.cppreference.com/w/c/string/byte/strtof)

-   itoa -<

    :   ```cpp
        #include <iostream>
        #include <algorithm>
        #include <stdio.h>

        const char* convert(char buf[], int value)
        {
            static char digits =
            { '9', '8', '7', '6', '5', '4', '3', '2', '1', '0',
                '1', '2', '3', '4', '5', '6', '7', '8', '9' };
            static const char *const zero = digits + 9; // zero 指向 '0'
            // works for -2147483648 .. 2147483647
            int i = value;
            char* p = buf;
            do {
                // lsd - least significant digit
                int lsd = i % 10; // lsd 可能小于 0
                // 是向下取整还是向零取整?
                *p++ = zero[lsd]; // 下标可能为负
                i /= 10;
            } while (i != 0);
            if (value < 0) {
                *p++ = '-';
            }
            *p = '\0';
            std::reverse(buf, p);
            return p; // p - buf 即为整数长度
        }

        int main() {
            char buf;
            int num;
            while( 1 == scanf( "%d", &num ) ) {
                convert( buf, num );
                printf( "%d -> \"%s\"\n", num, buf );
            }
        }
        ```

        ```bash
        $ g++ itoa.cpp -o itoa
        $ echo "23 235982743 -23432" | ./itoa
        23 -> "23"
        235982743 -> "235982743"
        -23432 -> "-23432"
        ```

        C 语言中的整数除法 (/) 和取模 (%) 运算在操作数为负的时候,结果是
        implementation-defined。也就是说,如果 m、d 都是整数,

        ```cpp
        int q = m / d;
        int r = m % d;
        ```

        那么 C 语言只保证 m = q × d + r。如果 m、d 当中有负数,那么 q 和 r 的正
        负号是由实现决定的。比如 (−13)/4 = (−3) 或 (−13)/4 = (−4) 都是合法的。
        如果采用后一种实现,那么这段转换代码就错了(因为将有 (−1)%10 = 9 )。只有
        商向 0 取整,代码才能正常工作。

        GCC always follows the C99 requirement that the result of division is
        truncated towards zero. G++ 一直遵循 C99 规范,商向 0 取整,算法能正常工作。

        Visual C++ 2008, The sign of the remainder is the same as the sign of
        the dividend.这个说法与商向 0 取整是等价的,算法也能正常工作。

-   scanf, fscanf, sscanf, scanf_s, fscanf_s, sscanf_s -<

    :   ```cpp
        size_t x = ...;
        ssize_t y = ...;
        printf("%zu\n", x);  // prints as unsigned decimal
        printf("%zx\n", x);  // prints as hex
        printf("%zd\n", y);  // prints as signed decimal
        ```

        -   `%s`, `%20s` （最多 scanf 20 char，需要有 21 长度的 buf）
        -   `%d` (decimal integer), strtol(ptr, &end, 10)
        -   `%i` (integer), strtol(ptr, &end, 0)
        -   `%u` (unsigned decimal integer), strtoul(ptr, &end, 10)
        -   `%o` (unsigned octal integer), strtoul(ptr, &end, 8)
        -   `%x`, `%X` (unsigned hexadecimal integer), strtoul(ptr, &end, 16)
        -   `%a`, `%A`, `%e`, `%E`, `%f`, `%F`, `%g`, `%G`, same as strtof
        -   `%p`, pointer
        -   输入和输出用的格式字符串不一样。输入 short 要用 %hd,输出用 %d;输入 double 要用 %lf,输出用 %f。

        正确而安全的做法如 Bjarne Stroustrup 在《Learning Standard C++ as a New Language》所示:

        ```cpp
        int main()
        {
            const int max_name = 80;
            char name[max_name];
            char fmt;
            sprintf(fmt, ”%%%ds”, max_name - 1);
            scanf(fmt, name);
            printf(”%s\n”, name);
        }
        ```

        ```cpp
        scanf("%d", &a);
        scanf(" %c", &c); // ignore the endline after %d, then read a char
        ```

        ```cpp
        #define __STDC_WANT_LIB_EXT1__ 1
        #include <stdio.h>
        #include <stddef.h>
        #include <locale.h>

        int main(void)
        {
            int i, j;
            float x, y;
            char str1, str2;
            wchar_t warr;
            setlocale(LC_ALL, "en_US.utf8");

            char input[] = "25 54.32E-1 Thompson 56789 0123 56ß水";
            /* parse as follows:
               %d: an integer
               %f: a floating-point value
               %9s: a string of at most 9 non-whitespace characters
               %2d: two-digit integer (digits 5 and 6)
               %f:  a floating-point value (digits 7, 8, 9)
               %*d: an integer which isn't stored anywhere
               ' ': all consecutive whitespace
               %3[0-9]: a string of at most 3 decimal digits (digits 5 and 6)
               %2lc: two wide characters, using multibyte to wide conversion  */
            int ret = sscanf(input, "%d%f%9s%2d%f%*d %3[0-9]%2lc",
                             &i, &x, str1, &j, &y, str2, warr);

            printf("Converted %d fields:\ni = %d\nx = %f\nstr1 = %s\n"
                   "j = %d\ny = %f\nstr2 = %s\n"
                   "warr = U+%x warr = U+%x\n",
                   ret, i, x, str1, j, y, str2, warr, warr);

        #ifdef __STDC_LIB_EXT1__
            int n = sscanf_s(input, "%d%f%s", &i, &x, str1, (rsize_t)sizeof str1);
            // writes 25 to i, 5.432 to x, the 9 bytes "thompson\0" to str1, and 3 to n.
        #endif
        }
        ```

        ```
        Converted 7 fields:
        i = 25
        x = 5.432000
        str1 = Thompson
        j = 56
        y = 789.000000
        str2 = 56
        warr = U+df warr = U+6c34
        ```

        ```cpp
        sprintf(dst, "%s and %s", dst, t); // <- broken: undefined behavior

        const char *fmt = "sqrt(2) = %f";
        int sz = snprintf(NULL, 0, fmt, sqrt(2));
        char buf[sz + 1]; // note +1 for terminating null byte
        snprintf(buf, sizeof buf, fmt, sqrt(2));
        ```

        refs and see also

        -   [c - How can one print a size_t variable portably using the printf family? - Stack Overflow](http://stackoverflow.com/questions/2524611/how-can-one-print-a-size-t-variable-portably-using-the-printf-family)
        -   [scanf, fscanf, sscanf, scanf_s, fscanf_s, sscanf_s - cppreference.com](http://en.cppreference.com/w/c/io/fscanf)
        -   [printf, fprintf, sprintf, snprintf, printf_s, fprintf_s - cppreference.com](http://en.cppreference.com/w/c/io/fprintf)
        -   [vscanf, vfscanf, vsscanf, vscanf_s, vfscanf_s, vsscanf_s - cppreference.com](http://en.cppreference.com/w/c/io/vfscanf)

-   `T::iterator`{.cpp}, `T::const_iterator`{.cpp}, `begin`{.cpp}, `end`{.cpp} -<

    :   -   begin, end, cbegin, cend, rbegin, rend
        -   iterator, const_iterator, reverse_iterator

        ```cpp
        template<typename Container, typename Function>
        void for_each(Container&& cont, Function f) {
            using std::begin;
            auto it = begin(cont);
            using std::end;
            auto end_it = end(cont);
            while (it != end_it) {
                f(*it);
                ++it;
            }
        }
        ```

        ```cpp
        #include <iostream>
        #include <vector>
        #include <iterator>

        int main()
        {
            std::vector<int> v = { 3, 1, 4 };
            auto vi = std::begin(v);
            std::cout << *vi << '\n';

            int a[] = { -5, 10, 15 };
            auto ai = std::begin(a);
            std::cout << *ai << '\n';
        }
        ```

        `std::reverse_iterator<std::string::iterator> r = s.rbegin()`{.cpp} -<

        :   ![](http://upload.cppreference.com/mwiki/images/3/39/range-rbegin-rend.svg)

            ```cpp
            #include <iostream>
            #include <string>
            #include <iterator>

            int main()
            {
                std::string s = "Hello, world";
                std::reverse_iterator<std::string::iterator> r = s.rbegin();
                r = 'O'; // replaces 'o' with 'O'
                r += 7; // iterator now points at 'O'
                std::string rev(r, s.rend());
                std::cout << rev << '\n'; // "OlleH"
            }
            ```

        refs and see also

        -   [std::iterator - cppreference.com](http://en.cppreference.com/w/cpp/iterator/iterator)
        -   [std::begin - cppreference.com](http://en.cppreference.com/w/cpp/iterator/begin)
        -   [std::end - cppreference.com](http://en.cppreference.com/w/cpp/iterator/end)
        -   [std::reverse_iterator - cppreference.com](http://en.cppreference.com/w/cpp/iterator/reverse_iterator)
        -   [std::ostream_iterator - cppreference.com](http://en.cppreference.com/w/cpp/iterator/ostream_iterator)

-   `std::min`, `std::max`, `std::minmax` -<

    :   ```cpp
        // misc
        std::max(1, 9999)
        std::max('a', 'b')
        std::max( { "foo", "bar", "hello" },
                [](const std::string& s1, const std::string& s2) {
                    return s1.size() < s2.size();
                });
        ```

        ```cpp
        template<class T, class Compare>
        std::pair<const T&, const T&> minmax( const T& a, const T& b, Compare comp )
        {
            return comp(b, a) ? std::pair<const T&, const T&>(b, a)
                              : std::pair<const T&, const T&>(a, b);
        }
        ```

        ```cpp
        #include <algorithm>
        #include <iostream>
        #include <vector>
        #include <cstdlib>
        #include <ctime>

        int main()
        {
            std::vector<int> v {3, 1, 4, 1, 5, 9, 2, 6};
            // or, std::vector<int> v = {3, 1, 4, 1, 5, 9, 2, 6};
            std::srand(std::time(0));
            std::pair<int, int> bounds = std::minmax(std::rand() % v.size(),
                                                     std::rand() % v.size());

            std::cout << "v[" << bounds.first << "," << bounds.second << "]: ";
            for (int i = bounds.first; i < bounds.second; ++i) {
                std::cout << v[i] << ' ';
            }
            std::cout << '\n';
        }

        /*
        Possible output:

        v[2,7]: 4 1 5 9 2
        */
        ```

        refs and see also

        -   [std::max - cppreference.com](http://en.cppreference.com/w/cpp/algorithm/max)
        -   [std::minmax - cppreference.com](http://en.cppreference.com/w/cpp/algorithm/minmax)

-   `std::map`{.cpp} -<

    :   `std::map`{.cpp} is a sorted associative container that contains key-value pairs
        with unique keys. Keys are sorted by using the comparison function Compare.
        Search, removal, and insertion operations have logarithmic complexity. Maps
        are usually implemented as red-black trees.

        refs and see also

        -   [std::map - cppreference.com](http://en.cppreference.com/w/cpp/container/map)

-   `std::partial_sum` -<

    :   ```
        // Defined in header <numeric>
        *(d_first)   = *first;
        *(d_first+1) = *first + *(first+1);
        *(d_first+2) = *first + *(first+1) + *(first+2);
        *(d_first+3) = *first + *(first+1) + *(first+2) + *(first+3);
        ...
        ```

        ```cpp
        #include <iostream>
        #include <sstream>
        #include <iterator>
        #include <numeric>

        int main()
        {
            std::istringstream str("0.1 0.2 0.3 0.4");
            std::partial_sum( std::istream_iterator<double>(str),
                              std::istream_iterator<double>(),
                              std::ostream_iterator<double>(std::cout, " ") );
        }
        ```

        Output: `0.1 0.3 0.6 1`

-   `std::equal_range` -<

    :   Returns a range containing all elements equivalent to value in the range
        `[first, last)`.

        ```cpp
        template<class ForwardIt, class T, class Compare>
        std::pair<ForwardIt,ForwardIt>
            equal_range(ForwardIt first, ForwardIt last,
                        const T& value, Compare comp);
        {
            return std::make_pair(std::lower_bound(first, last, value, comp),
                                  std::upper_bound(first, last, value, comp));
        }
        ```

        ```cpp
        #include <algorithm>
        #include <vector>
        #include <iostream>

        struct S
        {
            int number;
            char name;

            S ( int number, char name  )
                : number ( number ), name ( name )
            {}

            // only the number is relevant with this comparison
            bool operator< ( const S& s ) const
            {
                return number < s.number;
            }
        };


        int main()
        {
            // note: not ordered, only partitioned w.r.t. S defined below
            std::vector<S> vec = { {1,'A'}, {2,'B'}, {2,'C'}, {2,'D'}, {4,'G'}, {3,'F'} };

            S value ( 2, '?' );

            auto p = std::equal_range(vec.begin(),vec.end(),value);

            for ( auto i = p.first; i != p.second; ++i )
                std::cout << i->name << ' ';
                // 输出为 B C D
        }
        ```

        ```cpp
        #include <cstddef>
        #include <new>
        #include <vector>
        #include <iostream>

        // minimal C++11 allocator with debug output
        template <class Tp>
        struct NAlloc {
            typedef Tp value_type;
            NAlloc() = default;
            template <class T> NAlloc(const NAlloc<T>&) {}
            Tp* allocate(std::size_t n) {
                n *= sizeof(Tp);
                std::cout << "allocating " << n << " bytes\n";
                return static_cast<Tp*>(::operator new(n));
            }
            void deallocate(Tp* p, std::size_t n) {
                std::cout << "deallocating " << n*sizeof*p << " bytes\n";
                ::operator delete(p);
            }
        };
        template <class T, class U>
        bool operator==(const NAlloc<T>&, const NAlloc<U>&) { return true; }
        template <class T, class U>
        bool operator!=(const NAlloc<T>&, const NAlloc<U>&) { return false; }

        int main()
        {
            int sz = 100;
            std::cout << "using reserve: \n";
            {
                std::vector<int, NAlloc<int>> v1;
                v1.reserve(sz);
                for(int n = 0; n < sz; ++n)
                    v1.push_back(n);
            }
            std::cout << "not using reserve: \n";
            {
                std::vector<int, NAlloc<int>> v1;
                for(int n = 0; n < sz; ++n)
                    v1.push_back(n);
            }
        }
        ```

        Possible output:

        ```
        using reserve:
        allocating 400 bytes
        deallocating 400 bytes
        not using reserve:
        allocating 4 bytes
        allocating 8 bytes
        deallocating 4 bytes
        allocating 16 bytes
        deallocating 8 bytes
        allocating 32 bytes
        deallocating 16 bytes
        allocating 64 bytes
        deallocating 32 bytes
        allocating 128 bytes
        deallocating 64 bytes
        allocating 256 bytes
        deallocating 128 bytes
        allocating 512 bytes
        deallocating 256 bytes
        deallocating 512 bytes
        ```

        refs and see also

        -   [std::equal_range - cppreference.com](http://en.cppreference.com/w/cpp/algorithm/equal_range)

-   `upper_bound` & `lower_bound` -<

    :   Returns an iterator pointing to the first element in the range
        `[first, last)` that is greater than value.

        ```cpp
        #include <algorithm>
        #include <iostream>
        #include <iterator>
        #include <vector>

        int main()
        {
            std::vector<int> data = { 1, 1, 2, 3, 3, 3, 3, 4, 4, 4, 5, 5, 6 };

            auto lower = std::lower_bound(data.begin(), data.end(), 4); // 从右往左，看能不能再低，找不到？那就 begin
            auto upper = std::upper_bound(data.begin(), data.end(), 4); // 从左往右，看能不能再高，找不到？那就 end

            std::copy(lower, upper, std::ostream_iterator<int>(std::cout, " "));
        }
        ```

        output: 4 4 4

        ```cpp
        template<class ForwardIt, class T>
        ForwardIt upper_bound(ForwardIt first, ForwardIt last, const T& value)
        {
            ForwardIt it;
            typename std::iterator_traits<ForwardIt>::difference_type count, step;
            count = std::distance(first,last);

            while (count > 0) {
                it = first;
                step = count / 2;
                std::advance(it, step);
                if (!(value < *it)) {
                    first = ++it;
                    count -= step + 1;
                } else count = step;
            }
            return first;
        }
        ```

        refs and see also

        -   [std::upper_bound - cppreference.com](http://en.cppreference.com/w/cpp/algorithm/upper_bound)
        -   [std::partial_sum - cppreference.com](http://en.cppreference.com/w/cpp/algorithm/partial_sum)

-   `std::transform` -<

    :   ```cpp
        #include <string>
        #include <cctype>
        #include <algorithm>
        #include <iostream>

        int main()
        {
            std::string s("hello");
            // std::transform( s.begin(), s.end(), s.begin(), ::toupper );
            std::transform( s.begin(), s.end(), s.begin(),
                            [](unsigned char c) { return std::toupper(c); } );
            std::cout << s;
        }
        ```

        refs and see also

        -   [std::transform - cppreference.com](http://en.cppreference.com/w/cpp/algorithm/transform)

-   `std::prev`, `std::next` -<

    :   ```cpp
        template<class ForwardIt>
        ForwardIt next(ForwardIt it,
                       typename std::iterator_traits<ForwardIt>::difference_type n = 1)
        {
            std::advance(it, n);
            return it;
        }
        ```

        ```cpp
        #include <iostream>
        #include <iterator>
        #include <vector>

        int main()
        {
            std::vector<int> v{ 3, 1, 4 };

            auto it = v.begin();

            auto nx = std::next(it, 2);

            std::cout << *it << ' ' << *nx << '\n';
        }
        ```

        ```cpp
        std::vector<int> v{ 3, 1, 4 };
        std::cout << *std::prev(v.end(), 3) << '\n';
        ```

        refs and see also

        -   [std::prev - cppreference.com](http://en.cppreference.com/w/cpp/iterator/prev)
        -   [std::next - cppreference.com](http://en.cppreference.com/w/cpp/iterator/next)

-   `std::advance` -<

    :   Increments given iterator it by n elements.

        If n is negative, the iterator is decremented. In this case, InputIt must
        meet the requirements of BidirectionalIterator, otherwise the behavior is
        undefined.

        ```
        #include <iostream>
        #include <iterator>
        #include <vector>

        int main()
        {
            std::vector<int> v{ 3, 1, 4 };

            auto vi = v.begin();

            std::advance(vi, 2);

            std::cout << *vi << '\n';
        }
        // output: 4
        ```

        refs and see also

        -   [std::advance - cppreference.com](http://en.cppreference.com/w/cpp/iterator/advance)

-   `std::distance` -<

    :   Defined in header `<iterator>`

        ```cpp
        #include <iostream>
        #include <iterator>
        #include <vector>

        int main()
        {
            std::vector<int> v{ 3, 1, 4 };
            std::cout << "distance(first, last) = "
                      << std::distance(v.begin(), v.end()) << '\n'
                      << "distance(last, first) = "
                      << std::distance(v.end(), v.begin()) << '\n';
        }

        /*
         *  Output:
         *
         *              distance(first, last) = 3
         *              distance(last, first) = -3
        **/
        ```

        refs and see also

        -   [std::distance - cppreference.com](http://en.cppreference.com/w/cpp/iterator/distance)

-   `std::remove`, `std::remove_if`{.heart} -<

    :   ```cpp
        #include <algorithm>
        #include <string>
        #include <iostream>
        #include <cctype>

        int main()
        {
            std::string str1 = "Text with some   spaces";
            str1.erase(std::remove(str1.begin(), str1.end(), ' '),
                       str1.end());
            std::cout << str1 << '\n';

            std::string str2 = "Text\n with\tsome \t  whitespaces\n\n";
            str2.erase(std::remove_if(str2.begin(),
                                      str2.end(),
                                      [](char x){return std::isspace(x);}),
                       str2.end());
            std::cout << str2 << '\n';
        }
        ```

        Output:

        ```
        Textwithsomespaces
        Textwithsomewhitespaces
        ```

        ```
        const int n = 10;
        int A[n];
        std::remove_if(A, A+n, ' ');
        ```

        refs and see also

        -   [std::remove, std::remove_if - cppreference.com](http://en.cppreference.com/w/cpp/algorithm/remove)

-   `std::copy` -<

    :   Copies the elements in the range, defined by [first, last), to another
        range beginning at d_first.

        -   1) Copies all elements in the range [first, last). The behavior is
            undefined if d_first is within the range [first, last). In this case,
            std::copy_backward may be used instead.
        -   3) Only copies the elements for which the predicate pred returns true.
            The order of the elements that are not removed is preserved. The behavior
            is undefined if the source and the destination ranges overlap.
        -   2,4) Same as (1,3), but executed according to policy. These overloads do
            not participate in overload resolution unless
            std::is_execution_policy_v<std::decay_t<ExecutionPolicy>> is true

        ```cpp
        #include <algorithm>
        #include <iostream>
        #include <vector>
        #include <iterator>
        #include <numeric>

        int main()
        {
            std::vector<int> from_vector(10);
            std::iota(from_vector.begin(), from_vector.end(), 0);

            std::vector<int> to_vector;
            std::copy(from_vector.begin(), from_vector.end(),
                      std::back_inserter(to_vector));   // 从后面插入
        // or, alternatively,
        //  std::vector<int> to_vector(from_vector.size());
        //  std::copy(from_vector.begin(), from_vector.end(), to_vector.begin());
        // either way is equivalent to
        //  std::vector<int> to_vector = from_vector;

            std::cout << "to_vector contains: ";

            std::copy(to_vector.begin(), to_vector.end(),
                      std::ostream_iterator<int>(std::cout, " "));
            std::cout << '\n';
        }
        ```

        output: to_vector contains: 0 1 2 3 4 5 6 7 8 9

        refs and see also

        -   [std::copy, std::copy_if - cppreference.com](http://en.cppreference.com/w/cpp/algorithm/copy)

-   `std::move` -<

    :   ```cpp
        #include <iostream>
        #include <utility>
        #include <vector>
        #include <string>

        int main()
        {
            std::string str = "Hello";
            std::vector<std::string> v;

            // uses the push_back(const T&) overload, which means
            // we'll incur the cost of copying str
            v.push_back(str);
            std::cout << "After copy, str is \"" << str << "\"\n";

            // uses the rvalue reference push_back(T&&) overload,
            // which means no strings will be copied; instead, the contents
            // of str will be moved into the vector.  This is less
            // expensive, but also means str might now be empty.
            v.push_back(std::move(str));
            std::cout << "After move, str is \"" << str << "\"\n";

            std::cout << "The contents of the vector are \"" << v
                                                 << "\", \"" << v << "\"\n";
        }
        ```

        output:

        ```
        After copy, str is "Hello"
        After move, str is ""
        The contents of the vector are "Hello", "Hello"
        ```

        refs and see also

        -   [std::move - cppreference.com](http://en.cppreference.com/w/cpp/utility/move)

-   `std::unique` -<

    :   ```cpp
        #include <iostream>
        #include <algorithm>
        #include <vector>
        #include <string>
        #include <cctype>

        int main()
        {
            // remove duplicate elements (normal use)
            std::vector<int> v{1,2,3,1,2,3,3,4,5,4,5,6,7};
            std::sort(v.begin(), v.end()); // 1 1 2 2 3 3 3 4 4 5 5 6 7
            auto last = std::unique(v.begin(), v.end());
            // v now holds {1 2 3 4 5 6 7 x x x x x x}, where 'x' is indeterminate
            v.erase(last, v.end());
            for (int i : v)
              std::cout << i << " ";
            std::cout << "\n";

            // remove consecutive spaces
            std::string s = "wanna go    to      space?";
            auto end = std::unique(s.begin(), s.end(), [](char l, char r){  // 相同？那我把 r 去掉。
                return std::isspace(l) && std::isspace(r) && l == r;
            });
            // s now holds "wanna go to space?xxxxxxxx", where 'x' is indeterminate
            std::cout << std::string(s.begin(), end) << '\n';
        }
        ```

        Output:

        ```
        1 2 3 4 5 6 7
        wanna go to space?
        ```

        这个实现没有看懂：

        ```cpp
        template<class ForwardIt, class BinaryPredicate>
        ForwardIt unique(ForwardIt first, ForwardIt last, BinaryPredicate p)
        {
            if (first == last)
                return last;

            ForwardIt result = first;
            while (++first != last) {
                if (!p(*result, *first) && ++result != first) {
                    *result = std::move(*first);
                }
            }
            return ++result;
        }
        ```

        refs and see also

        -   [std::unique - cppreference.com](http://en.cppreference.com/w/cpp/algorithm/unique)

-   `std::iota` -<

    :   Fills the range `[first, last)` with sequentially increasing values, starting
        with value and repetitively evaluating ++value.

        Equivalent operation:

        ```cpp
        *(d_first)   = value;
        *(d_first+1) = ++value;
        *(d_first+2) = ++value;
        *(d_first+3) = ++value;
        ...
        ```

        ```cpp
        #include <algorithm>
        #include <iostream>
        #include <list>
        #include <numeric>
        #include <random>
        #include <vector>

        int main()
        {
            std::list<int> l(10);
            std::iota(l.begin(), l.end(), -4);  // l 中的元素为：-4, -3, -2, -1, 0, 1, 2, 3, 4, 5

            std::vector<std::list<int>::iterator> v(l.size());
            std::iota(v.begin(), v.end(), l.begin());

            std::shuffle(v.begin(), v.end(), std::mt19937{std::random_device{}()});

            std::cout << "Contents of the list: ";
            for(auto n: l) std::cout << n << ' ';
            std::cout << '\n';

            std::cout << "Contents of the list, shuffled: ";
            for(auto i: v) std::cout << *i << ' ';
            std::cout << '\n';
        }
        ```

        Possible output:

        ```
        Contents of the list: -4 -3 -2 -1 0 1 2 3 4 5
        Contents of the list, shuffled: 0 -1 3 4 -4 1 -2 -3 2 5
        ```

        Iota `/aɪˈoʊtə/` (uppercase Ι, lowercase ι; Greek: Ιώτα) is the ninth
        letter of the Greek alphabet. It was derived from the Phoenician letter
        Yodh.

        For example, the integer function denoted by ι produces a vector of the
        first N integers when applied to the argument N, …

        refs and see also

        -   [std::iota - cppreference.com](http://en.cppreference.com/w/cpp/algorithm/iota)
        -   [c++ - What does iota of std::iota stand for? - Stack Overflow](http://stackoverflow.com/questions/9244879/what-does-iota-of-stdiota-stand-for) -<

-   numeric limits -<

    :   limits

        T    |   std::numeric_limits<T>::max()
        :---: | :---:
        /* non-specialized */    |   T();
        bool    |   true
        char    |   CHAR_MAX
        signed char    |   SCHAR_MAX
        unsigned char    |   UCHAR_MAX
        wchar_t    |   WCHAR_MAX
        char16_t    |   UINT_LEAST16_MAX
        char32_t    |   UINT_LEAST32_MAX
        short    |   SHRT_MAX
        unsigned short    |   USHRT_MAX
        int    |   INT_MAX
        unsigned int    |   UINT_MAX
        long    |   LONG_MAX
        unsigned long    |   ULONG_MAX
        long long    |   LLONG_MAX
        unsigned long long    |   ULLONG_MAX
        float    |   FLT_MAX
        double    |   DBL_MAX
        long double    |   LDBL_MAX

        refs and see also

        -   [std::numeric_limits::max - cppreference.com](http://en.cppreference.com/w/cpp/types/numeric_limits/max)

-   `std::vector::erase` -<

    :   synopsis

        -   `iterator erase( iterator pos );`
        -   `iterator erase( const_iterator pos );`
        -   `iterator erase( iterator first, iterator last );`
        -   `iterator erase( const_iterator first, const_iterator last );`

        ```cpp
        #include <vector>
        #include <iostream>

        int main( )
        {
            std::vector<int> c{0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
            for (auto &i : c) {
                std::cout << i << " ";
            }
            std::cout << '\n';

            c.erase(c.begin());

            for (auto &i : c) {
                std::cout << i << " ";
            }
            std::cout << '\n';

            c.erase(c.begin()+2, c.begin()+5);

            for (auto &i : c) {
                std::cout << i << " ";
            }
            std::cout << '\n';
        }
        ```

        refs and see also

        -   [std::vector::erase - cppreference.com](http://en.cppreference.com/w/cpp/container/vector/erase)

-   MISC, unordered_map, find, unordered_multimap -<

    :   ```cpp
        unordered_map<int, int> map;
        if (map.find(num[i]) != map.end()) { continue; }
        else { map[num[i]] = 1; }

        map.find(gap) != map.end() && map[gap] > i

        sort(num.begin(), num.end());

        vector<vector<int>> result;
        result.push_back({ 1, 2, 3 });

        unordered_map<int, vector<pair<int, int> > > cache;
        for (size_t a = 0; a < num.size(); ++a) {
            for (size_t b = a + 1; b < num.size(); ++b) {
                cache[num[a] + num[b]].push_back(pair<int, int>(a, b));
            }
        }

        unordered_multimap<int, pair<int, int>> cache;
        for (int i = 0; i + 1 < num.size(); ++i)
            for (int j = i + 1; j < num.size(); ++j)
                cache.insert(make_pair(num[i] + num[j], make_pair(i, j)));
            }
        }

        int trap(int A[], int n) {
            int *max_left = new int[n]();
            int *max_right = new int[n]();
            for (int i = 1; i < n; i++) {
                max_left[i] = max(max_left[i - 1], A[i - 1]);
                // 一下就把两边都考虑了……好牛逼！
                max_right[n - 1 - i] = max(max_right[n - i], A[n - i]);
            }
            int sum = 0;
            for (int i = 0; i < n; i++) {
                int height = min(max_left[i], max_right[i]);
                if (height > A[i]) {
                    sum += height - A[i];
                }
            }
            delete[] max_left;
            delete[] max_right;
            return sum;
        }

        class Solution  {
        public:
            vector<int> plusOne(vector<int> &digits) {
                add(digits, 1);
                return digits;
            }

        private:
            // 0 <= digit <= 9
            void add(vector<int> &digits, int digit) {
                int c = digit; // carry, 进位

                // method 1
                for (auto it = digits.rbegin(); it != digits.rend(); ++it) {
                    *it += c;
                    c = *it / 10;
                    *it %= 10;
                }

                // method 2
                for_each(digits.rbegin(), digits.rend(), [&c](int &d){
                    d += c;
                    c = d / 10;
                    d %= 10;
                });

                if (c > 0) { digits.insert(digits.begin(), 1) }; // 这里是 insert c 比较好。
            }
        }
        ```

-   "值语义" 与 "对象语义" -<

    :   不同于标准库其他 class 的“值语义/value semantics”,iostream 是“对象语义
        /object semantics” 42 ,即 iostream 是 non-copyable。这 是 正 确 的,因
        为 如 果fstream 代表一个打开的文件的话,拷贝一个 fstream 对象意味着什么
        呢?表示打开了两个文件吗?如果销毁一个 fstream 对象,它会关闭文件句柄,那么
        另一个fstream 对象副本会因此受影响吗?  iostream 禁止拷贝,利用对象的生命
        期来明确管理资源(如文件),很自然地就避免了这些问题。这就是 RAII,一种重要
        且独特的 C++ 编程手法。C++ 同 时 支 持 “数 据 抽 象/data abstraction”
        和 “面 向 对 象 编 程/object- oriented”,其实主要就是“值语义”与“对象语义
        ”的区别,这是一个比较大的话题,见12节。

        值 得 注 意 的 是, `istream::getline()` 成 员 函 数 的 参 数 类 型 是 char*, 因 为
        `<istream>` 没有包含 `<string>`,而我们常用的 `std::getline()` 函数是个 non-member
        function,定义在 <string> 里边。

        我们知道,面向对象中的 public 继承需要满足 Liskov 替换原则。(见《Effective
        C++ 第 3 版》[2, item 32]:确保你的 public 继承模塑出 is-a 关系。《C++ 编程规范》
        [4, item 37]:public 继承意味可替换性。继承非为复用,乃为被复用。)

## 快问快答 FAQ

-   struct 和 class 的区别？ -<

    :   **7.8 What's the difference between the keywords struct and class?**

        >   The members and base classes of a struct are **public by default**,
        >   while in class, they **default to private**. Note: you should make
        >   your base classes explicitly public, private, or protected, rather
        >   than relying on the defaults.
        >
        >   Struct and class are **otherwise functionally equivalent**.
        >
        >   OK, enough of that squeaky clean techno talk. Emotionally, most
        >   developers make a strong distinction between a class and a struct.
        >   A struct simply feels like an open pile of bits with very little in
        >   the way of encapsulation or functionality. A class feels like a
        >   living and responsible member of society with intelligent services,
        >   a strong encapsulation barrier, and a well defined interface. Since
        >   that's the connotation most people already have, you should
        >   probably use the struct keyword if you have a class that has very
        >   few methods and has public data (such things do exist in well
        >   designed systems!), but otherwise you should probably use the class
        >   keyword.

        According to Stroustrup in the C++ Programming Language:

        >   Which style you use depends on circumstances and taste. I usually
        >   prefer to use struct for classes that have all data public. I think
        >   of such classes as "not quite proper types, just data structures."

        Functionally, there is no difference other than the public / private

        “说 struct 和 class 类似只是为了让菜 b 容易理解”，
        那请问那位"高手"帮忙说出除了

        -   **访问权限**,
        -   **继承权限**, 和
        -   **template 里 type parameter 关键字可以用 class 不能用 struct**

        之外的其它不同之处吧。

        refs and see also

        -   [oop - What are the differences between struct and class in C++? - Stack Overflow](http://stackoverflow.com/questions/92859/what-are-the-differences-between-struct-and-class-in-c)
        -   [C++ FAQ (part 03 of 14)](http://www.faqs.org/faqs/C++-faq/part3/)
        -   [关于阿里巴巴2011年一道C++笔试题（struct和class）的疑问? - 知乎](https://www.zhihu.com/question/33700942)
        -   [C++中struct与class的区别 - 人在旅途 - 博客频道 - CSDN.NET](http://blog.csdn.net/nocky/article/details/6195556)

-   为什么 alignment 可以提高总线（bus）的运输效率？ -<

    :   It's a limitation of many underlying processors. It can usually be
        worked around by doing 4 inefficient single byte fetches rather than
        one efficient word fetch, but many language specifiers decided it would
        be easier just to outlaw them and force everything to be aligned.

        因为 bulk read/write memory 要快些。内存对齐了数据就拷贝得快了。

        [Data alignment: Straighten up and fly right](http://www.ibm.com/developerworks/library/pa-dalign/){.heart} -<

        :   ![](http://www.ibm.com/developerworks/library/pa-dalign/howProgrammersSeeMemory.jpg)
            ![](http://www.ibm.com/developerworks/library/pa-dalign/howProcessorsSeeMemory.jpg)

            Figure 1. How programmers see memory & Figure 2. How processors see memory

            However, your computer's processor does not read from and write to
            memory in byte-sized chunks. Instead, it accesses memory in two-,
            four-, eight- 16- or even 32-byte chunks. We'll call the size in which
            a processor accesses memory its memory access granularity.

            ![Figure 3. Single-byte memory access granularity（`[grænjʊ'lærɪtɪ]`，粒度）](http://www.ibm.com/developerworks/library/pa-dalign/singleByteAccess.jpg)

            你觉得就该这样？

            但是两个字节一读更快，四个还要快！

            ![Figure 4. Double-byte memory access granularity](http://www.ibm.com/developerworks/library/pa-dalign/doubleByteAccess.jpg)

            ![Figure 5. Quad-byte memory access granularity](http://www.ibm.com/developerworks/library/pa-dalign/quadByteAccess.jpg)

            ![Figure 6. How processors handle unaligned memory access](http://www.ibm.com/developerworks/library/pa-dalign/unalignedAccess.jpg)

            ![如果都不对齐的话……8 字节反而慢得要死。](http://www.ibm.com/developerworks/library/pa-dalign/horrorChartWhole.jpg)

            ![4 字节 align](http://www.ibm.com/developerworks/library/pa-dalign/horrorChartHeadless.jpg)

            ![8 字节 align，效果就更好了。](http://www.ibm.com/developerworks/library/pa-dalign/horrorChartHeadlessHilited.jpg)

        [ludx/The-Lost-Art-of-C-Structure-Packing: The Lost Art of C Structure Packing中文翻译](https://github.com/ludx/The-Lost-Art-of-C-Structure-Packing) -<

        :   字符数组 `pad` 意味着在这个结构体中，有3个字节的空间被浪费掉了。老派术语将其称之为**“废液（slop，`[slɑt]`）”**。
            首先，在此例中，N将为0，x的地址紧随p之后，能确保是与指针对齐的，因为指针的对齐要求总比int严格。

            倘若你希望这些变量占用的空间更少，那么可以交换x与c的次序。

            ```cpp
            char *p;     /* 8 bytes */
            long x;      /* 8 bytes */
            char c;      /* 1 byte */
            // 然而在我 x64 Linux 上，g++ 4.8 编译结果是 sizeof(struct) = 24
            // 内容并没有减少。
            ```

            ANSI C 提供了一个 `offsetof()` 宏，可用于读取结构体成员位移。（来自 stddef.h）

            （吐槽，offsetof 可能拿到 bigfield 的地址==，`&` 也不行。好把，地址都是按 byte 来的……）

            ```cpp
            #include <stdio.h>
            #include <stddef.h>

            struct X {
                    char *p;     /* 8 bytes */
                    char e;      /* 1 byte */
                    long x;      /* 8 bytes */
                    char c;      /* 1 byte */
                    char d;      /* 1 byte */
            };

            int main()
            {
                printf("offsetof(X, x): %d\n", offsetof(struct X, x));  // 16
            }
            ```

            现在，我们来谈谈结构体的**尾填充（trailing padding）**。为了解释它，需要引
            入一个基本概念，我将其称为结构体的“跨步地址（stride address）”。它是在
            结构体数据之后，与结构体对齐一致的首个地址。

            结构体尾填充的通用法则是：编译器将会对结构体进行尾填充，直至它的跨步地址。这条法则决定了sizeof()的返回值。

            即使在 64 位系统上，sizeof(foo4) 也是 4（而不是更浪费空间但更整齐的 8）。

            ```cpp
            struct foo4 {
                short s;     /* 2 bytes */
                char c;      /* 1 byte */
            };
            ```

            现在我们考虑位域（bitfields）。利用位域，你能声明比字符宽度更小的成员，低至１位，例如：

            ```cpp
            struct foo5 {
                short s;
                char c;
                int flip:1;
                int nybble:4;
                int septet:7;
            };
            ```

            关于位域需要了解的是，它们是由字（或字节）层面的掩码和移位指令实现的。
            从编译器的角度来看，struct foo5中的位域就像２字节、16位的字符数组，只用
            到了其中12位。为了使结构体的长度是其最宽成员长度sizeof(short)的整数倍，
            接下来进行了填充。

            ```cpp
            struct foo5 {
                short s;       /* 2 bytes */
                char c;        /* 1 byte */
                int flip:1;    /* total 1 bit */
                int nybble:4;  /* total 5 bits */
                int septet:7;  /* total 12 bits */
                int pad1:4;    /* total 16 bits = 2 bytes */
                char pad2;     /* 1 byte */
            };
            ```

            内层结构体成员char *p强迫外层结构体与内层结构体指针对齐一致。在64位系统
            中，实际的内存分布将类似这样：

            ```cpp
            struct foo6 {
                char c;           /* 1 byte */
                char pad1;     /* 7 bytes */
                struct foo6_inner {
                    char *p;      /* 8 bytes */
                    short x;      /* 2 bytes */
                    char pad2; /* 6 bytes */
                } inner;
            };
            ```

            理解了编译器在结构体中间和尾部插入填充的原因与方式后，我们来看看如何榨
            出这些废液。此即结构体打包的技艺。

            消除废液最简单的方式，是按对齐值递减重新对结构体成员排序。即让所有指针
            对齐成员排在最前面，因为在64位系统中它们占用8字节；然后是4字节的int；再
            然后是2字节的short，最后是字符。

            笨拙地、机械地重排结构体可能有损可读性。倘若有可能，最好这样重排成员：
            将语义相关的数据放在一起，形成连贯的组。最理想的情况是，结构体的设计应
            与程序的设计相通。

            最冒险的打包方法是使用union。假如你知道结构体中的某些域永远不会跟另一些
            域共同使用，可以考虑用union共享它们存储空间。不过请特别小心并用回归测试
            验证。因为如果分析出现一丁点儿错误，就会引发从程序崩溃到微妙数据损坏（
            这种情况糟得多）间的各种错误。

            clang编译器有个Wpadded选项，可以生成有关对齐和填充的信息。

        rms's code to test memory alignment -<

        :   see [packtest.c](http://www.catb.org/esr/structure-packing/packtest.c) -<

            ```cpp
            #include <stdio.h>
            #include <stdbool.h>

            struct foo1 {
                char *p;
                char c;
                long x;
            };
            // sizeof(struct foo1)   = 24

            struct foo2 {
                char c;      /* 1 byte */
                char pad; /* 7 bytes */
                char *p;     /* 8 bytes */
                long x;      /* 8 bytes */
            };
            // sizeof(struct foo2)   = 24

            struct foo3 {
                char *p;     /* 8 bytes */
                char c;      /* 1 byte */
            };
            // sizeof(struct foo3)   = 16

            struct foo4 {
                short s;     /* 2 bytes */
                char c;      /* 1 byte */
            };
            // sizeof(struct foo4)   = 4

            struct foo5 {
                char c;
                struct foo5_inner {
                    char *p;
                    short x;
                } inner;
            };
            // sizeof(struct foo5)   = 24

            struct foo6 {
                short s;
                char c;
                int flip:1;
                int nybble:4;
                int septet:7;
            };
            // sizeof(struct foo6)   = 8

            struct foo7 {
                int bigfield:31;
                int littlefield:1;
            };
            // sizeof(struct foo7)   = 4

            struct foo8 {
                int bigfield1:31;
                int littlefield1:1;
                int bigfield2:31;
                int littlefield2:1;
            };
            // sizeof(struct foo8)   = 8

            struct foo9 {
                int bigfield1:31;
                int bigfield2:31;
                int littlefield1:1;
                int littlefield2:1;
            };
            // sizeof(struct foo9)   = 12


            struct foo10 {
                char c;
                struct foo10 *p;
                short x;
            };
            // sizeof(struct foo10)   = 24

            struct foo11 {
                struct foo11 *p;
                short x;
                char c;
            };
            // sizeof(struct foo11)   = 16

            struct foo12 {
                struct foo12_inner {
                    char *p;
                    short x;
                } inner;
                char c;
            };
            // sizeof(struct foo12)   = 24

            main(int argc, char *argv)
            {
                printf("sizeof(char *)        = %zu\n", sizeof(char *));
                printf("sizeof(long)          = %zu\n", sizeof(long));
                printf("sizeof(int)           = %zu\n", sizeof(int));
                printf("sizeof(short)         = %zu\n", sizeof(short));
                printf("sizeof(char)          = %zu\n", sizeof(char));
                printf("sizeof(float)         = %zu\n", sizeof(float));
                printf("sizeof(double)        = %zu\n", sizeof(double));
                printf("sizeof(struct foo1)   = %zu\n", sizeof(struct foo1));
                printf("sizeof(struct foo2)   = %zu\n", sizeof(struct foo2));
                printf("sizeof(struct foo3)   = %zu\n", sizeof(struct foo3));
                printf("sizeof(struct foo4)   = %zu\n", sizeof(struct foo4));
                printf("sizeof(struct foo5)   = %zu\n", sizeof(struct foo5));
                printf("sizeof(struct foo6)   = %zu\n", sizeof(struct foo6));
                printf("sizeof(struct foo7)   = %zu\n", sizeof(struct foo7));
                printf("sizeof(struct foo8)   = %zu\n", sizeof(struct foo8));
                printf("sizeof(struct foo9)   = %zu\n", sizeof(struct foo9));
                printf("sizeof(struct foo10)   = %zu\n", sizeof(struct foo10));
                printf("sizeof(struct foo11)   = %zu\n", sizeof(struct foo11));
                printf("sizeof(struct foo12)   = %zu\n", sizeof(struct foo12));
            }
            ```

            output:

            ```
            sizeof(char *)        = 8
            sizeof(long)          = 8
            sizeof(int)           = 4
            sizeof(short)         = 2
            sizeof(char)          = 1
            sizeof(float)         = 4
            sizeof(double)        = 8
            sizeof(struct foo1)   = 24
            sizeof(struct foo2)   = 24
            sizeof(struct foo3)   = 16
            sizeof(struct foo4)   = 4
            sizeof(struct foo5)   = 24
            sizeof(struct foo6)   = 8
            sizeof(struct foo7)   = 4
            sizeof(struct foo8)   = 8
            sizeof(struct foo9)   = 12
            sizeof(struct foo10)   = 24
            sizeof(struct foo11)   = 16
            sizeof(struct foo12)   = 24
            ```

        Computer Systems: A Programmer's Perspective: -<

        :   >   **3.9.3 Data Alignment**
            >
            >   Many computer systems place restrictions on the allowable
            >   addresses for the primitive data types, requiring that the
            >   address for some type of object must be a multiple of some
            >   value K (typically 2, 4, or 8). Such alignment restrictions
            >   **simplify the design of the hardware** forming the interface
            >   between the processor and the memory system. For example,
            >   suppose a processor always fetches 8 bytes from memory with an
            >   address that must be a multiple of 8. If we can guarantee that
            >   any double will be aligned to have its address be a multiple of
            >   8, then the value can be read or written with a single memory
            >   operation. Otherwise, we may need to perform two memory
            >   accesses, since the object might be split across two 8-byte
            >   memory blocks.
            >
            >   The IA32 hardware will work correctly regardless of the
            >   alignment of data. However, Intel recommends that data be
            >   aligned to improve memory system performance. Linux follows an
            >   alignment policy where 2-byte data types (e.g., short) must
            >   have an address that is a multiple of 2, while any larger data
            >   types (e.g., int, int *, float, and double) must have an
            >   address that is a multiple of 4. Note that this requirement
            >   means that the least significant bit of the address of an
            >   object of type short must equal zero. Similarly, any object of
            >   type int, or any pointer, must be at an address having the
            >   low-order 2 bits equal to zero.

        refs and see also

        -   [Purpose of memory alignment - Stack Overflow](http://stackoverflow.com/questions/381244/purpose-of-memory-alignment)
        -   [memory management - Compelling examples of custom C++ allocators? - Stack Overflow](http://stackoverflow.com/questions/826569/compelling-examples-of-custom-c-allocators)

-   为什么 fread、fwrite 要两个参数（sizeof(element) + number of element） -<

    :   fread、fwrite 的 signature 是：

        ```cpp
        size_t fread(        void *ptr, size_t size, size_t nmemb, FILE *stream );
        size_t fwrite( const void *ptr, size_t size, size_t nmemb, FILE *stream );
        ```

        其中返回值是成功读取、写出的 nmemb 数。这一点有助于查错。

        `scanf` 会返回 scanf 到的变量数目；`printf` 会返回 print 出的 char 数目（不包括哪个 terminating null char `'\0'`）。

        refs and see also

        -   [c - Significance of two arguments in fread? - Stack Overflow](http://stackoverflow.com/questions/29861811/significance-of-two-arguments-in-fread)
        -   [c - What is the rationale for fread/fwrite taking size and count as arguments? - Stack Overflow](http://stackoverflow.com/questions/295994/what-is-the-rationale-for-fread-fwrite-taking-size-and-count-as-arguments/#296018)

-   What and where are the stack and heap? -<

    :   The stack is the memory set aside as scratch space for a thread of
        execution. When a function is called, a block is reserved on the top of
        the stack for local variables and some bookkeeping data. When that
        function returns, the block becomes unused and can be used the next
        time a function is called. The stack is always reserved in a LIFO (last
        in first out) order; the most recently reserved block is always the
        next block to be freed. This makes it really simple to keep track of
        the stack; freeing a block from the stack is nothing more than
        adjusting one pointer.

        The heap is memory set aside for dynamic allocation. Unlike the stack,
        there's no enforced pattern to the allocation and deallocation of
        blocks from the heap; you can allocate a block at any time and free it
        at any time. This makes it much more complex to keep track of which
        parts of the heap are allocated or free at any given time; there are
        many custom heap allocators available to tune heap performance for
        different usage patterns.

        Each thread gets a stack, while there's typically only one heap for the
        application (although it isn't uncommon to have multiple heaps for
        different types of allocation).

        To answer your questions directly:

        To what extent are they controlled by the OS or language runtime?

        :   The OS allocates the stack for each system-level thread when the
            thread is created. Typically the OS is called by the language
            runtime to allocate the heap for the application.

        What is their scope?

        :   The stack is attached to a thread, so when the thread exits the
            stack is reclaimed. The heap is typically allocated at application
            startup by the runtime, and is reclaimed when the application
            (technically process) exits.

        What determines the size of each of them?

        :   The size of the stack is set when a thread is created. The size of
            the heap is set on application startup, but can grow as space is
            needed (the allocator requests more memory from the operating
            system).

        What makes one faster?

        :   The stack is faster because the access pattern makes it trivial to
            allocate and deallocate memory from it (a pointer/integer is simply
            incremented or decremented), while the heap has much more complex
            bookkeeping involved in an allocation or deallocation. Also, each
            byte in the stack tends to be reused very frequently which means it
            tends to be mapped to the processor's cache, making it very fast.
            Another performance hit for the heap is that the heap, being mostly
            a global resource, typically has to be multi-threading safe, i.e.
            each allocation and deallocation needs to be - typically -
            synchronized with "all" other heap accesses in the program.

        A clear demonstration:

        :   ![](http://i.stack.imgur.com/i6k0Z.png)

        Stack:

        -   Stored in computer RAM just like the heap.
        -   Variables created on the stack will **go out of scope and automatically deallocate**.
        -   Much **faster to allocate** in comparison to variables on the heap.
        -   Implemented with an actual stack data structure.
        -   Stores local data, return addresses, used for parameter passing
        -   Can have a stack overflow when too much of the stack is used. (mostly from infinite (or too much) recursion, very large allocations)
        -   Data created on the stack can be used without pointers.
        -   **You would use the stack if you know exactly how much data you need to allocate before compile time and it is not too big.**
        -   Usually has a maximum size already determined when your program starts

        Heap:

        -   Stored in computer RAM just like the stack.
        -   **In C, variables on the heap must be destroyed manually and never fall out of scope.** The data is freed with `delete`, `delete[]`, or `free`
        -   **Slower to allocate** in comparison to variables on the stack.
        -   Used on demand to allocate a block of data for use by the program.
        -   Can have fragmentation when there are a lot of allocations and deallocations
        -   In C++ data created on the heap will be pointed to by pointers and allocated with new or malloc
        -   Can have allocation failures if too big of a buffer is requested to be allocated.
        -   You would use the heap if you don't know exactly how much data you will need at runtime or if you need to allocate a lot of data.
        -   Responsible for memory leaks

        ```cpp
        int foo()
        {
          char *pBuffer; //<--nothing allocated yet (excluding the pointer itself, which is allocated here on the stack).
          bool b = true; // Allocated on the stack.
          if(b)
          {
            //Create 500 bytes on the stack
            char buffer;

            //Create 500 bytes on the heap
            pBuffer = new char;

           }//<-- buffer is deallocated here, pBuffer is not
        }//<--- oops there's a memory leak, I should have called delete[] pBuffer;
        ```

        refs and see also

        -   [memory management - What and where are the stack and heap? - Stack Overflow](http://stackoverflow.com/questions/79923/what-and-where-are-the-stack-and-heap#79936)

-   pointer 和 reference 的区别？What are the differences between a pointer variable and a reference variable in C++? -<

    :   -   A pointer can be re-assigned any number of times while a reference can not be re-seated after binding.
        -   Pointers can point nowhere (NULL), whereas reference always refer to an object.
        -   You can't take the address of a reference like you can with pointers.
        -   There's no "reference arithmetics" (but you can take the address of
            an object pointed by a reference and do pointer arithmetics on it
            as in `&obj + 5`).
        -   References cannot be stuffed into an array, whereas pointers can be

        You can have pointers to pointers to pointers offering extra levels of
        indirection. Whereas references only offer one level of indirection.

        ```cpp
        int x = 0;
        int y = 0;
        int *p = &x;
        int *q = &y;
        int **pp = &p;
        pp = &q;//*pp = q
        **pp = 4;
        assert(y == 4);
        assert(x == 0);
        ```

        A pointer is a variable that holds a memory address. Regardless of how
        a reference is implemented, a reference has the same memory address as
        the item it references.

        ```cpp
        int i = 2, *pi = &i, &ri = i;
        printf( "%p, %p, %p, %p\n", &i, pi, &ri, &pi );
        // 0x7fff039457c4, 0x7fff039457c4, 0x7fff039457c4, 0x7fff039457c8

        // &&ri --> error: label ‘ri’ used but not defined
        ```

        Const references can be bound to temporaries. Pointers cannot (not without some indirection):

        ```cpp
        const int &x = int(12); //legal C++
        int *y = &int(12); //illegal to dereference a temporary.
        ```

        This makes `const&` safer for use in argument lists and so forth.

        refs and see also

        -   [What are the differences between a pointer variable and a reference variable in C++? - Stack Overflow](http://stackoverflow.com/questions/57483/what-are-the-differences-between-a-pointer-variable-and-a-reference-variable-in)
        -   [C++ FQA Lite: References](http://yosefk.com/c++fqa/ref.html)

-   What is the difference between `const int*`, `const int * const`, and `int const *`? -<

    :   Read it backwards (as driven by **Clockwise/Spiral Rule**)...

        -   `int*` - pointer to int
        -   `int const *` - pointer to const int
        -   `int * const` - const pointer to int
        -   `int const * const` - const pointer to const int

        Now the first const can be on either side of the type so:

        -   `const int *` == `int const *`
        -   `const int * const` == `int const * const`

        If you want to go really crazy you can do things like this:

        -   `int **` - pointer to pointer to int
        -   `int ** const` - a const pointer to a pointer to an int
        -   `int * const *` - a pointer to a const pointer to an int
        -   **`int const **`** - a pointer to a pointer to a const int （这个有点容易弄错）
        -   `int * const * const` - a const pointer to a const pointer to an int
            ...

        And to make sure we are clear on the meaning of const

        ```cpp
        const int* foo;         // 不能改变指向位置的内容
        int *const bar = &i;    // 只能指向 i，不能 re-assign 了
        ```

        ```cpp
        // pc 指向一个 const char，所以不能修改 buf
        const char *pc = buf;
        // pc 指向 buf2，依旧不能修改，因为不管 pc 指向哪儿，它都不能修改它所指的对象
        pc = buf2;
        ```

        refs and see also

        -   [c++ - What is the difference between `const int*`, `const int * const`, and `int const *`? - Stack Overflow](http://stackoverflow.com/questions/1143262/what-is-the-difference-between-const-int-const-int-const-and-int-const)
        -   [Clockwise/Spiral Rule](http://c-faq.com/decl/spiral.anderson.html)

-   What are rvalues, lvalues, xvalues, glvalues, and prvalues? -<

    :   ```
                    expression
                     /    \
                    /      \
                   /        \
               glvalue     rvalue
               /    \      /    \
              /      \    /      \
             /        \  /        \
          lvalue     xvalue     prvalue

                    ______ ______
                   /      X      \
                  /      / \      \
                 |   l  | x |  pr  |
                  \      \ /      /
                   \______X______/
                       gl    r

        ```

        In C++03, an expression is either an rvalue or an lvalue.

        In C++11, an expression can be an:

        -   rvalue  = prvalue - xvalue
        -   glvalue = lvalue + xvalue

        Two categories have become five categories. What are these new categories of expressions?
        How do these new categories relate to the existing rvalue and lvalue categories?
        Are the rvalue and lvalue categories in C++0x the same as they are in C++03?
        Why are these new categories needed? Are the WG21 gods just trying to confuse us mere mortals?

        -   An lvalue (so-called, historically, because lvalues could appear on the
            left-hand side of an assignment expression) designates a function or an
            object.

            Example: If E is an expression of pointer type, then *E is an
            lvalue expression referring to the object or function to which E points. As
            another example, **the result of calling a function whose return type is an
            lvalue reference is an lvalue.**

        -   An xvalue (**an “eXpiring” value**) also refers to an object, usually near the
            end of its lifetime (so that its resources may be moved, for example). An
            xvalue is the result of certain kinds of expressions involving rvalue
            references.

            Example: The result of calling a function whose return type is
            an rvalue reference is an xvalue.

        -   A glvalue (“generalized” lvalue) is an lvalue or an xvalue.

        -   An rvalue (so-called, historically, because rvalues could appear on the
            right-hand side of an assignment expression) is an xvalue, a
            **temporary object** or subobject thereof（其中的）, or a value
            that is not associated with an object.

        -   A prvalue (“pure” rvalue) is an rvalue that is not an xvalue.

            Example: The result of calling a function whose return type is not
            a reference is a prvalue

        Examples with functions:

        ```cpp
        int   prvalue();
        int&  lvalue();
        int&& xvalue();
        ```

        But also don't forget that named rvalue references are lvalues:

        ```cpp
        void foo(int&& t) {
          // t is initialized with an rvalue expression
          // but is actually an lvalue expression itself
        }
        ```

        refs and see also

        -   [c++ - What are rvalues, lvalues, xvalues, glvalues, and prvalues? - Stack Overflow](http://stackoverflow.com/questions/3601602/what-are-rvalues-lvalues-xvalues-glvalues-and-prvalues)
        -   [n3055.pdf](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2010/n3055.pdf)
        -   [Value categories - cppreference.com](http://en.cppreference.com/w/cpp/language/value_category){.heart}

-   Iterator invalidation rules -<

    :   refs and see also

        -   [c++ - Iterator invalidation rules - Stack Overflow](http://stackoverflow.com/questions/6438086/iterator-invalidation-rules)

-   Split a string in C++? -<

    :   What's the most elegant way to split a string in C++? The string can be
        assumed to be composed of words separated by whitespace.

        ```cpp
        #include <iostream>
        #include <sstream>
        #include <string>

        using namespace std;

        int main()
        {
            string s("Somewhere down the road");
            istringstream iss(s);

            do
            {
                string sub;
                iss >> sub;
                cout << "Substring: " << sub << endl;
            } while (iss);
        }
        ```

        Dude... Elegance is just a fancy way to say
        "efficiency-that-looks-pretty" in my book. Don't shy away from using C
        functions and quick methods to accomplish anything just because it is
        not contained within a template ;)

        ```cpp
        #include <iostream>
        #include <string>
        #include <sstream>
        #include <algorithm>
        #include <iterator>

        int main() {
            using namespace std;
            string sentence = "And I feel fine...";

            {
                istringstream iss(sentence);
                copy(istream_iterator<string>(iss),
                        istream_iterator<string>(),
                        ostream_iterator<string>(cout, " | "));
                cout << "\n";
            }
            {
                istringstream iss(sentence);
                vector<string> tokens{istream_iterator<string>{iss},
                                      istream_iterator<string>{}};
                for( const string &s : tokens ) {
                    cout << "(" << s << ") ";
                }
                cout << "\n";
            }
            {
                istringstream iss(sentence);
                vector<string> tokens;
                copy(istream_iterator<string>(iss),
                     istream_iterator<string>(),
                     back_inserter(tokens));
                for( const string &s : tokens ) {
                    cout << "[" << s << "] ";
                }
                cout << "\n";
            }
        }
        ```

        output:

        ```
        And | I | feel | fine... |
        (And) (I) (feel) (fine...)
        [And] [I] [feel] [fine...]
        ```

        ```cpp
        #include <string>
        #include <sstream>
        #include <vector>

        using namespace std;

        void split(const string &s, char delim, vector<string> &elems) {
            stringstream ss(s);
            string item;
            while (getline(ss, item, delim)) {
                elems.push_back(item);
            }
        }


        vector<string> split(const string &s, char delim) {
            vector<string> elems;
            split(s, delim, elems);
            return elems;
        }
        ```

        ```cpp
        #include <boost/algorithm/string.hpp>
        std::vector<std::string> strs;
        boost::split(strs, "string to split", boost::is_any_of("\t "));
        ```

        strtok?

        refs and see also

        -   [Split a string in C++? - Stack Overflow](http://stackoverflow.com/questions/236129/split-a-string-in-c)

-   What does the explicit keyword in C++ mean? -<

    :   In C++, the compiler is allowed to make one implicit conversion to
        resolve the parameters to a function. What this means is that the
        compiler can use constructors callable with a single parameter to
        convert from one type to another in order to get the right type for a
        parameter. Here's an example class with a constructor that can be used
        for implicit conversions:

        ```cpp
        class Foo
        {
        public:
          // single parameter constructor, can be used as an implicit conversion
          Foo (int foo) : m_foo (foo)
          {
          }

          int GetFoo () { return m_foo; }

        private:
          int m_foo;
        };
        ```

        Here's a simple function that takes a Foo object:

        ```cpp
        void DoBar (Foo foo)
        {
          int i = foo.GetFoo ();
        }
        ```

        and here's where the DoBar function is called.

        ```cpp
        int main ()
        {
          DoBar (42);
        }
        ```

        The argument is not a Foo object, but an int. However, there exists a
        constructor for Foo that takes an int so this constructor can be used
        to convert the parameter to the correct type.

        The compiler is allowed to do this once for each parameter.

        Prefixing the explicit keyword to the constructor prevents the compiler
        from using that constructor for implicit conversions. Adding it to the
        above class will create a compiler error at the function call DoBar
        (42). It is now necessary to call for conversion explicitly with  DoBar
        (Foo (42))

        The reason you might want to do this is to avoid accidental
        construction that can hide bugs. Contrived example:

        >   You have a MyString(int size) class with a constructor that
        >   constructs a string of the given size. You have a function
        >   print(const MyString&), and you call it with print(3). You expect
        >   it to print "3", but it prints an empty string of length 3 instead.

        nice write up, you might want to mention multi-arg ctors with default
        params can also act as single arg ctor, e.g., `Object( const char* name=NULL, int otype=0)`.

        I think it should also be mentioned that one should consider making
        single argument constructors explicit initially (more or less
        automatically), and removing the explicit keyword only when the
        implicit conversion is wanted by design. I think contructors should be
        explicit by default with an 'implicit' keyword to enable them to work
        as implicit conversions. But that's not how it is. – Michael Burr Aug
        26 '09 at 17:47

        Just an FYI that when calling "print(3)" in your example, the function
        needs to be "print(const MyString &"). The "const" is mandatory here
        because 3 is converted to a temporary "MyString" object and you can't
        bind a temporary to a reference unless it's "const" (yet another in a
        long list of C++ gotchas)

        ---

        Suppose you have a class String:

        ```cpp
        class String {
        public:
            String(int n); // allocate n bytes to the String object
            String(const char *p); // initializes object with char *p
        };
        ```

        Now if you try

        ```cpp
        String mystring = 'x';
        ```

        the char 'x' will be implicitly converted to int and then will call the
        String(int) constructor. But this is not what the user might have
        intended. So to prevent such conditions, we shall define the
        constructor as explicit:

        ```cpp
        class String {
        public:
            explicit String (int n); //allocate n bytes
            String(const char *p); // initialize sobject with string p
        };
        ```

        refs and see also

        -   [constructor - What does the explicit keyword in C++ mean? - Stack Overflow](http://stackoverflow.com/questions/121162/what-does-the-explicit-keyword-in-c-mean)

-   When should `static_cast`, `dynamic_cast`, `const_cast` and `reinterpret_cast` be used? -<

    :   -   static_cast

            :   static_cast is the first cast you should attempt to use. It
                does things like implicit conversions between types (such as
                int to float, or pointer to void*), and it can also call
                explicit conversion functions (or implicit ones). In many
                cases, explicitly stating static_cast isn't necessary, but it's
                important to note that the T(something) syntax is equivalent to
                (T)something and should be avoided (more on that later). A
                T(something, something_else) is safe, however, and guaranteed
                to call the constructor.

                static_cast can also cast through inheritance hierarchies. It
                is unnecessary when casting upwards (towards a base class), but
                when casting downwards it can be used as long as it doesn't
                cast through virtual inheritance. It does not do checking,
                however, and it is undefined behavior to static_cast down a
                hierarchy to a type that isn't actually the type of the object.

        -   const_cast

            :   const_cast can be used to remove or add const to a variable; no
                other C++ cast is capable of removing it (not even
                reinterpret_cast). It is important to note that modifying a
                formerly const value is only undefined if the original variable
                is const; if you use it to take the const off a reference to
                something that wasn't declared with const, it is safe. This can
                be useful when overloading member functions based on const, for
                instance. It can also be used to add const to an object, such
                as to call a member function overload.

                const_cast also works similarly on volatile, though that's less
                common.

        -   dynamic_cast

            :   dynamic_cast is almost exclusively used for handling
                polymorphism. You can cast a pointer or reference to any
                polymorphic type to any other class type (a polymorphic type
                has at least one virtual function, declared or inherited). You
                can use it for more than just casting downwards -- you can cast
                sideways or even up another chain. The dynamic_cast will seek
                out the desired object and return it if possible. If it can't,
                it will return nullptr in the case of a pointer, or throw
                std::bad_cast in the case of a reference.

                dynamic_cast has some limitations, though. It doesn't work if
                there are multiple objects of the same type in the inheritance
                hierarchy (the so-called 'dreaded diamond') and you aren't
                using virtual inheritance. It also can only go through public
                inheritance - it will always fail to travel through protected
                or private inheritance. This is rarely an issue, however, as
                such forms of inheritance are rare.

        -   reinterpert_cast

            :   reinterpret_cast is the most dangerous cast, and should be used
                very sparingly. It turns one type directly into another - such
                as casting the value from one pointer to another, or storing a
                pointer in an int, or all sorts of other nasty things. Largely,
                the only guarantee you get with reinterpret_cast is that
                normally if you cast the result back to the original type, you
                will get the exact same value (but not if the intermediate type
                is smaller than the original type). There are a number of
                conversions that reinterpret_cast cannot do, too. It's used
                primarily for particularly weird conversions and bit
                manipulations, like turning a raw data stream into actual data,
                or storing data in the low bits of an aligned pointer.

        -   C casts

            :   C casts are casts using (type)object or type(object). A C-style
                cast is defined as the first of the following which succeeds:

                -   const_cast
                -   static_cast (though ignoring access restrictions)
                -   static_cast (see above), then const_cast
                -   reinterpret_cast
                -   reinterpret_cast, then const_cast

                It can therefore be used as a replacement for other casts in
                some instances, but can be extremely dangerous because of the
                ability to devolve into a reinterpret_cast, and the latter
                should be preferred when explicit casting is needed, unless you
                are sure static_cast will succeed or reinterpret_cast will
                fail. Even then, consider the longer, more explicit option.

                C-style casts also ignore access control when performing a
                static_cast, which means that they have the ability to perform
                an operation that no other cast can. This is mostly a kludge,
                though, and in my mind is just another reason to avoid C-style
                casts.

        so, the rules are:

        -   Use dynamic_cast for converting pointers/references within an
            inheritance hierarchy.
        -   Use static_cast for ordinary type conversions.
        -   Use reinterpret_cast for low-level reinterpreting of bit patterns.
            Use with extreme caution.
        -   Use const_cast for casting away const/volatile. Avoid this unless
            you are stuck using a const-incorrect API.

        refs and see also

        -   [c++ - When should static_cast, dynamic_cast, const_cast and reinterpret_cast be used? - Stack Overflow](http://stackoverflow.com/questions/332030/when-should-static-cast-dynamic-cast-const-cast-and-reinterpret-cast-be-used)

-   Operator overloading -<

    :   refs and see also

        -   [c++ - Operator overloading - Stack Overflow](http://stackoverflow.com/questions/4421706/operator-overloading)

-   如何理解 C 语言关键字 restrict？ -<

    :   [Pointer aliasing - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Pointer_aliasing)

        :   In computer programming, aliasing refers to the situation where the
            **same memory location can be accessed using different names**.

            In C99, the `restrict` keyword was added, which specifies that a
            pointer argument does not alias any other pointer argument.

        ```cpp
        int foo(int *a, int *b)
        {
            *a = 5;
            *b = 6;
            return *a + *b; // 不一定是 11！
        }
        ```

        如果 a 和 b 都指向同一数据，`*b = 6` 会导致 `*a = 6`，返回 12。所以编译器在
        做 `*a + *b` 的时候，需要重新读取 `*a` 指向的数据

        如果我们确保两个指针不指向同一数据，就可以用 restrict 修饰指针类型：

        ```cpp
        int rfoo(int *restrict a, int *restrict b)
        {
            *a = 5;
            *b = 6;
            return *a + *b;
        }
        ```

        但如果用了 restrict 去修饰两个指针，而它们在作用域内又指向同一地址，那
        么是未定义行为。

        总括而言，**restrict 是为了告诉编译器额外信息（两个指针不指向同一数据），
        从而生成更优化的机器码**。注意，编译器是无法自行在编译期检测两个指针是否
        alias。如使用 restrict，程序员也要遵守契约才能得出正确的代码（指针不能
        指向相同数据）。

        编译器就可以根据这个信息，做出优化。

        refs and see also

        -   [如何理解 C 语言关键字 restrict？ - 知乎](https://www.zhihu.com/question/41653775)
        -   [restrict type qualifier - cppreference.com](http://en.cppreference.com/w/c/language/restrict)

-   弱类型、强类型、动态类型、静态类型语言的区别是什么？ -<

    :   先定义一些基础概念

        Program Errors

        -   trapped errors。导致程序终止执行，如除 0，Java 中数组越界访问
        -   untrapped errors。 出错后继续执行，但可能出现任意行为。如 C 里的缓冲区溢出、Jump 到错误地址

        Forbidden Behaviours

        -   语言设计时，可以定义一组forbidden behaviors. 它必须包括所有untrapped
            errors, 但可能包含trapped errors.

        Well behaved、ill behaved

        -   well behaved: 如果程序执行不可能出现forbidden behaviors, 则为well behaved。
        -   ill behaved: 否则为ill behaved...

        有了上面的概念，再讨论强、弱类型，静态、动态类型

        强、弱类型

        -   强类型strongly typed: 如果一种语言的所有程序都是well behaved——即不可能出现forbidden behaviors，则该语言为strongly typed。
        -   弱类型weakly typed: 否则为weakly typed。比如C语言的缓冲区溢出，属于trapped errors，即属于forbidden behaviors..故C是弱类型

        前面的人也说了，弱类型语言，类型检查更不严格，如偏向于容忍隐式类型转换。譬如说C
        语言的int可以变成double。 这样的结果是：容易产生forbidden behaviours，所以是弱
        类型的

        动态、静态类型

        -   静态类型 statically: 如果在编译时拒绝ill behaved程序，则是statically typed;
        -   动态类型dynamiclly: 如果在运行时拒绝ill behaviors, 则是dynamiclly typed。

        误区

        -   大家觉得C语言要写int a, int b之类的，Python不用写(可以直接写a, b)，所以C是
            静态，Python是动态。这么理解是不够准确的。譬如Ocaml是静态类型的，但是也可以
            不用明确地写出来。。Ocaml是静态隐式类型

        静态类型可以分为两种：

        -   如果类型是语言语法的一部分，在是explicitly typed显式类型；
        -   如果类型通过编译时推导，是implicity typed隐式类型, 比如ML和Haskell

        下面是些例子

        -   无类型： 汇编
        -   弱类型、静态类型 ： C/C++
        -   弱类型、动态类型检查： Perl/PHP
        -   强类型、静态类型检查 ：Java/C#
        -   强类型、动态类型检查 ：Python, Scheme
        -   静态显式类型 ：Java/C
        -   静态隐式类型 ：Ocaml, Haskell

        by vczh

        :   -   强类型：偏向于不容忍隐式类型转换。譬如说haskell的int就不能变成double
            -   弱类型：偏向于容忍隐式类型转换。譬如说C语言的int可以变成double
            -   静态类型：编译的时候就知道每一个变量的类型，因为类型错误而不能做的事情是语法错误。
            -   动态类型：编译的时候不知道每一个变量的类型，因为类型错误而不能做的事情是运行时错误。譬如说你不能对一个数字a写a当数组用。

        refs and see also

        -   [弱类型、强类型、动态类型、静态类型语言的区别是什么？ - 知乎](https://www.zhihu.com/question/19918532)

-   几个真正的快问快答 -<

    :   -   How can I provide input for my class `Fred`?

            :   By adding a friend `std::istream& operator>> (std::istream& i, Fred& fred);`

        -   Should I end my output lines with `std::endl` or `\n`?

            :   The former has the additional side-effect of flushing the output buffer.
                Therefore, the latter will probably work faster.

                两者打印出来的换行符是一样的，系统相关的。

        -   How does that funky `while (std::cin >> foo)` syntax work?

            :   `istream` has overloaded `operator void*`. The compiler calls
                this operator in **boolean contexts** (when it expects a
                condition, for example), because `void*` can be converted to a
                boolean. The operator returns `NULL` when there's nothing left to
                read, or when a format error occurred previously.

                There's nothing "funky" about it. It is ugly and boring. It's
                also scary because many people think this is what programming
                is all about - using complicated syntax to do simple things
                without even getting them right (how do you tell end-of-file
                conditions from format errors?).

                Why is it operator `void*`, and not operator `bool`? Apparently
                because the compiler implicitly converts booleans to numbers in
                "numeric contexts" (such as `file1+file2`), and we don't want
                that to compile, do we?

                But wait, there's more! There's an actual book out there,
                called **"Imperfect C++"**, arguing that operator `void*` is not the
                way to go, either. Because this way, `delete file` would compile.
                Surely we don't want it to, do we? I mean, the fact that the
                statement is completely moronic（`[mɔ'rɔnik, mə-]` adj. 低能的；迟钝的）
                shouldn't matter. Morones have a right to get an equal
                opportunity in the exciting world of C++ programming; let's
                catch of all their errors at compile time. Evil people spread
                rumors about the problem being undecidable, but we should keep trying.

        -   Why should I use `<iostream>` instead of the traditional `<cstdio>`?

            :   There are four reasons:

                -   **Increase type safety**: with iostream, the compiler knows the
                    types of the things you print. stdio only figures them out at
                    run time from the format string.
                -   **Reduce the number of errors**: with stdio, the types of objects
                    you pass must be consistent with the format string; iostream
                    removes this redundancy - there is no format string, so you
                    can't make these errors.
                -   **Printing objects of user-defined types**: with iostream, you can
                    overload the operators << and >> to support new types, and the
                    old code won't break. stdio won't let you extend the format
                    string syntax, and there seems to be no way to support this
                    kind of thing in a way avoiding conflicts between different
                    extensions.
                -   **Printing to streams of user-defined types**: you can implement
                    your own stream classes by deriving from the base classes
                    provided by iostream. FILE* can not be extended because "it's
                    not a real class".

                其实前两点根本没有说服力。后两点呢……虽然 printf 不能自定义类型
                （新的诸如"%d"之类的标签），但是你可以自己写一个函数，把类型打
                印到 string 或者 console。然后再用 `std::cout` 输出。

        refs and see also

        -   [C++ FQA Lite: Input/output via <iostream> and <cstdio>](http://yosefk.com/c++fqa/io.html#fqa-15.14)

-   TODO -<

    :   - [c++ - What is object slicing? - Stack Overflow](http://stackoverflow.com/questions/274626/what-is-object-slicing)
        - [c++ faq - The Definitive C++ Book Guide and List - Stack Overflow](http://stackoverflow.com/questions/388242/the-definitive-c-book-guide-and-list)
        - [c++ - What is The Rule of Three? - Stack Overflow](http://stackoverflow.com/questions/4172722/what-is-the-rule-of-three)
        - [c++ - What is the copy-and-swap idiom? - Stack Overflow](http://stackoverflow.com/questions/3279543/what-is-the-copy-and-swap-idiom)
        - [c++ - What are move semantics? - Stack Overflow](http://stackoverflow.com/questions/3106110/what-are-move-semantics)
        - [c++ - Undefined behavior and sequence points - Stack Overflow](http://stackoverflow.com/questions/4176328/undefined-behavior-and-sequence-points)
        - [c++ - Do the parentheses after the type name make a difference with new? - Stack Overflow](http://stackoverflow.com/questions/620137/do-the-parentheses-after-the-type-name-make-a-difference-with-new)
        - [Highest Voted 'c++-faq' Questions - Stack Overflow](http://stackoverflow.com/questions/tagged/c%2b%2b-faq?sort=votes&pageSize=100)
        -   [Issues · ReadingLab/Discussion-for-Cpp](https://github.com/ReadingLab/Discussion-for-Cpp/issues)
        -   [pezy/QtLab: Qt Primer](https://github.com/pezy/QtLab)
        -   [Red–black tree - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree)
        -   [Pocket: My List](https://getpocket.com/a/queue/)
        -   [Sorting algorithm - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Sorting_algorithm)
        - [Heap · Data Structure and Algorithm notes](http://algorithm.yuanbin.me/zh-hans/basics_data_structure/heap.html)

## 关键概念 | Concepts

-   [Abstraction layer - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Abstraction_layer) -<

    :   ![A typical vision of a computer architecture as a series of abstraction
        layers: hardware, firmware, assembler, kernel, operating system and
        applications (see also ).](https://upload.wikimedia.org/wikipedia/commons/thumb/0/03/Computer_abstraction_layers.svg/200px-Computer_abstraction_layers.svg.png)

        In computing, an abstraction layer or abstraction level is a way of
        **hiding the implementation details** of a particular set of functionality,
        allowing the separation of concerns to facilitate interoperability and
        platform independence. Software models that use layers of abstraction
        include the OSI 7-layer model for computer network protocols, the
        OpenGL graphics drawing library, and the byte stream input/output (I/O)
        model originated from Unix and adopted by MS-DOS, Linux, and most other
        modern operating systems.

-   [Access modifiers - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Access_modifiers) -<

    :   **Access modifiers** (or access specifiers) are keywords in object-oriented
        languages that set the accessibility of classes, methods, and other members.
        Access modifiers are a specific part of programming language
        syntax used to facilitate the encapsulation of components.

        ```cpp
        #include <iostream>
        using std::cout;
        using std::endl;

        struct B { // default access modifier inside struct is public
            void set_n(int v) { n = v; }
            void f()          { cout << "B::f" << endl; }
          protected:
            int m, n; // B::m, B::n are protected
          private:
            int x;
        };

        struct D : B {
            using B::m;               // D::m is public
            int get_n() { return n; } // B::n is accessible here, but not outside
        //  int get_x() { return x; } // ERROR, B::x is inaccessible here
         private:
            using B::f;               // D::f is private
        };

        int main() {
            D d;

        //  d.x = 2; // ERROR, private          （B 的 private，D 无法访问）
        //  d.n = 2; // ERROR, protected        （B 的 protected，D 只能内部访问，B 的 protected，自己也只能内部访问。）
            d.m = 2; // protected B::m is accessible as D::m
                     //                         （B 的 protected，但是成了 D 的 public）

            d.set_n(2); // calls B::set_n(int)  （内部访问，set & get）
            cout << d.get_n() << endl; // output: 2

        //  d.f();   // ERROR, B::f is inaccessible as D::f
                     //                         （private 函数，限内部使用）

            B& b = d; // b references d and "views" it as being type B

        //  b.x = 3; // ERROR, private
        //  b.n = 3; // ERROR, protected
        //  b.m = 3; // ERROR, B::m is protected

            b.set_n(3); // calls B::set_n(int)  （虽然实际上是 d，但是类型是 B，所以用的 B::set_n）
        //  cout << b.get_n(); // ERROR, 'struct B' has no member named 'get_n'

            b.f();   // calls B::f()
            return 0;
        }
        ```

-   [Friend function - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Friend_function) -<

    :   In object-oriented programming, a friend function that is a "friend" of a
        given class is **allowed access to private and protected data** in that class
        that it would not normally be able to as if the data was public.  Normally,
        a function that is defined outside of a class cannot access such
        information. Declaring a function a friend of a class allows this, in
        languages where the concept is supported.

        A friend function is declared by the class that is granting access,
        explicitly stating what function from a class is allowed access. A similar
        concept is that of friend class.

        Friends should be used with caution. Too many functions or external classes
        declared as friends of a class with protected or private (visibility modes)
        data may lessen the value of encapsulation of separate classes in
        object-oriented programming and may indicate a problem in the overall
        architecture design. Generally though, friend functions are a good thing
        for encapsulation, as you can
        **keep data of a class private from all except those who you explicitly state need it**,
        but this does mean your classes will become tightly coupled.

        ```cpp
        #include <iostream>
        using namespace std;

        class Foo;
        class Bar {
          private:
              int a;
          public:
              Bar(): a(0) {}
              //                                    和 Foo 一样，有这个 friend
              void show(Bar& x, Foo& y);        //  私有函数
              friend void show(Bar& x, Foo& y); //  declaration of global friend，一个全局的朋友
        };

        class Foo {
          private:
              int b;
          public:
              Foo(): b(6) {}
              //                                    和 Bar 一样，有这个 friend
              friend void show(Bar& x, Foo& y); //  declaration of global friend
              // 还把 Bar 里面的 show 函数给 friend 的了。
              friend void Bar::show(Bar& x, Foo& y); // declaration of friend from other class
              //
              //  如果不 friend 这个函数，Bar 那个函数没法编译。会有如下错误：
              //
              //    main.cpp: In member function 'void Bar::show(Bar&, Foo&)':
              //    main.cpp:17: error: 'int Foo::b' is private
              //    main.cpp:29: error: within this context
        };

        // Definition of a member function of Bar; this member is a friend of Foo
        void Bar::show(Bar& x, Foo& y) {
        // 因为这个函数是 Foo 指定 friend 的，所以 Bar 可以访问 y.b (Foo::b).
          cout << "Show via function member of Bar" << endl;
          cout << "Bar::a = " << x.a << endl;
          cout << "Foo::b = " << y.b << endl;
        }

        // Friend for Bar and Foo, definition of global function
        void show(Bar& x, Foo& y) {
          cout << "Show via global function" << endl;
          cout << "Bar::a = " << x.a << endl;
          cout << "Foo::b = " << y.b << endl;
        }

        int main() {
           Bar a;
           Foo b;

           show(a,b);
           a.show(a,b); // Bar::show,
        }
        ```

        ```cpp
        // 更常用的呢，是 friend operator<<
        class Y {
            int data; // private member
            // the non-member function operator<< will have access to Y's private members
            friend std::ostream& operator<<(std::ostream& out, const Y& o);
            friend char* X::foo(int); // members of other classes can be friends too

            // 还可以连续 friend 两个函数。
            friend X::X(char), X::~X(); // constructors and destructors can be friends
        };
        // friend declaration does not declare a member function
        // this operator<< still needs to be defined, as a non-member
        std::ostream& operator<<(std::ostream& out, const Y& y)
        {
            return out << y.data; // can access private member Y::data
        }
        ```

        ```cpp
        class Y {};
        class A {
            int data; // private data member
            class B { }; // private nested type
            enum { a = 100 }; // private enumerator
            friend class X; // friend class forward declaration (elaborated class name)
            friend Y; // friend class declaration (simple type specifier) (since c++11)
        };

        class X : A::B // Error until C++11: the base-clause is not part of member declarations
                       // allowed in C++11
        {
            A::B mx; // OK: A::B accessible to member of friend
            class Y : A::B { // OK: A::B accessible to base-clause of nested member of friend
            };
            int v[A::a]; // OK: A::a accessible to member of friend
        };
        ```

        Notes

        -   Friendship is **not transitive** (a friend of your friend is not your
            friend)
        -   Friendship is **not inherited** (your friend's children are not your
            friends)
        -   Prior to C++11, member declarations and definitions inside the nested
            class of the friend of class T cannot access the private and protected
            members of class T, but some compilers accept it even in pre-C++11
            mode.
        -   Storage class specifiers are not allowed in friend function
            declarations. A function that is defined in the friend declaration has
            external linkage, a function that was previously defined, keeps the
            linkage it was defined with.
        -   **Access specifiers have no effect on the meaning of friend declarations**
            (they can appear in private: or in public: sections, with no difference)
        -   A friend class declaration cannot define a new class
            (`friend class X {};` is an error，`friend class X;` 是可以的。)
        -   When a local class declares an unqualified function or class as a
            friend, only functions and classes in the innermost non-class scope are
            looked up, not the global functions:

            ```cpp
            class F {};
            int f();
            int main()
            {
                extern int g();
                class Local { // Local class in the main() function
                    friend int f(); // Error, no such function declared in main()
                    friend int g(); // OK, there is a declaration for g in main()

                    // 即使 F 还没定义，也没声明，现在也可以 friend
                    friend class F; // friends a local F (defined later)
                    // 全剧的那个，可以用 ::F
                    friend class ::F; // friends the global F
                };
                class F {}; // local F
            }
            ```

        Template friends -<

        :   tl;dr

            ```cpp
            class A {
                template<typename T>
                friend class B;                 // every B<T> is a friend of A

                template<typename T>
                friend void f(T) {}             // every f<T> is a friend of A
            };
            ```

            Friend declarations cannot refer to **partial specializations**, but can
            refer to full specializations:

            ```cpp
            template<class T> class A {};               // primary
            template<class T> class A<T*> {};           // partial
            template<> class A<int> {};                 // full
            class X {
                template<class T> friend class A<T*>;   // error!
                friend class A<int>;                    // OK
            };
            ```

            When a friend declaration refers to a full specialization of a function
            template, the keyword inline and default arguments cannot be used.

            ```cpp
            template<class T> void f(int);
            template<> void f<int>(int);

            class X {
                friend void f<int>(int x = 1); // error: default args not allowed
            };
            ```

            ...

        refs and see also

        -   [friend declaration - cppreference.com](http://en.cppreference.com/w/cpp/language/friend)
        -   [friend (C++)](https://msdn.microsoft.com/en-us/library/465sdshe.aspx)

-   [Template (C++) - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Template_%28C%2B%2B%29) -<

    :   Templates are a feature of the C++ programming language that allows
        functions and classes to operate with generic types. This allows a function
        or class to work on many different data types without being rewritten for
        each one.

        Templates are of great utility to programmers in C++, especially when
        combined with multiple inheritance and operator overloading. The C++
        Standard Library provides many useful functions within a framework of
        connected templates.

        Major inspirations for C++ templates were the parameterized modules
        provided by CLU and the generics provided by Ada.

        There are three kinds of templates: **function templates**,
        **class templates** and, since C++14, **variable templates**.
        Since C++11, templates may be either variadic or non-variadic; in earlier
        versions of C++ they are always non-variadic.

        Function templates -<

        :   A function template behaves like a function except that the template
            can **have arguments of many different types** (see example). In other
            words, a function template represents a family of functions. The format
            for declaring function templates with type parameters is

            ```cpp
            // 两者等价，但第一种可能有误导（不必须是 class）
            template <class identifier> function_declaration;
            template <typename identifier> function_declaration;
            ```

            ```cpp
            template <typename Type>
            Type max(Type a, Type b) {
                return a > b ? a : b;
            }

            #include <iostream>
            int main()
            {
                // call max<int> by implicit argument deduction.
                std::cout << max(3, 7) << std::endl;

                // max<double>
                std::cout << max(3.0, 7.0) << std::endl;

                // This depends on the compiler. Some compilers handle this by defining a template
                // function like double max <double> ( double a, double b);, while in some compilers
                // we need to explicitly cast it, like std::cout << max<double>(3,7.0);
                std::cout << max(3, 7.0) << std::endl;
                std::cout << max<double>(3, 7.0) << std::endl;
                return 0;
            }
            ```

            This function template can be **instantiated**（`[ɪns'tænʃɪet]`，实例化） with any copy-constructible type
            for which the expression `y > x` is valid. For user-defined types, this
            implies that the greater-than operator (>) must be overloaded in the type.

        Class templates -<

        :   A class template **provides a specification for generating classes based on parameters**.
            Class templates are generally used to implement containers.  A class
            template is instantiated by passing a given set of types to it as
            template arguments. The C++ Standard Library contains many class
            templates, in particular the containers adapted from the Standard
            Template Library, such as vector.

        Variable templates

        :   In C++14, templates can be also used for variables, as in the following example.

            ```cpp
            template<typename T> constexpr T pi = T(3.1415926535897932385);

            // Usual specialization rules apply:
            template<> constexpr const char* pi<const char*> = "pi";
            ```

            see more at [C++14 - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/C%2B%2B14#Variable_templates).

        Template specialization -<

        :   When a function or class is instantiated from a template, a
            specialization of that template is created by the compiler for the set
            of arguments used, and the specialization is referred to as being a
            generated specialization.

            ```cpp
            template <>
            bool max<bool>(bool a, bool b) {
                return a || b;
            }
            ```

        Variadic templates -<

        :   C++11 introduced variadic templates, which can take a variable number of
            arguments in a manner somewhat similar to variadic functions such as
            `std::printf`{.cpp}. Both function templates and class templates can be variadic.

            [Variadic template - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Variadic_template) -<

            :   In computer programming, variadic templates are templates that take
                a variable number of arguments.  Variadic templates are supported
                by C++ (since the C++11 standard), and the D programming language.

                ```cpp
                template<typename... Values> class tuple;

                tuple<int, std::vector<int>, std::map<<std::string>, std::vector<int>>> some_instance_name;
                tuple<> some_instance_name;     // no arguments

                template<typename First, typename... Rest> class tuple; // at least one argument
                ```

                Variadic templates may also apply to functions, thus not only
                providing a type-safe add-on to variadic functions (such as printf)
                - but also allowing a printf-like function to process non-trivial
                objects.

                ```cpp
                template<typename... Params> void printf(const std::string &str_format, Params... parameters);
                ```

-   [Type system - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Type_system){.heart} -<

    :   In programming languages, a type system is **a collection of rules that
        assign a property called type** to various constructs a computer program
        consists of, such as variables, expressions, functions or modules. The
        main purpose of a type system is to **reduce possibilities for bugs** in
        computer programs by defining interfaces between different parts of a
        computer program, and then checking that the parts have been connected in a
        consistent way. This checking can happen statically (at compile time),
        dynamically (at run time), or as a combination of static and dynamic
        checking. Type systems have other purposes as well, such as enabling
        certain compiler optimizations, allowing for multiple dispatch, providing a
        form of documentation, etc.

        A type system associates a type with each computed value and, by examining
        the flow of these values, **attempts to ensure or prove that no type errors
        can occur**. The particular type system in question determines exactly what
        constitutes a type error, but in general the aim is to prevent operations
        expecting a certain kind of value from being used with values for which
        that operation does not make sense (**logic errors**); **memory errors** will also
        be prevented. Type systems are often specified as part of programming
        languages, and built into the interpreters and compilers for them; although
        the type system of a language can be extended by optional tools that
        perform additional kinds of checks using the language's original type
        syntax and grammar.


        [Fundamental types - cppreference.com](http://en.cppreference.com/w/cpp/language/types) -<

        :   -   Void type

                `void`，不能用来实例化对象，但可以用到 pointer，返回值，还可以用来消除 warning（比如 `(void)argc; (void)argv;`）

                `std::nullptr_t` 定义为 `typedef decltype(nullptr) nullptr_t;`。nullptr 和 NULL 类似。

                `std::nullptr_t` is the type of the null pointer literal, nullptr.
                It is a distinct type that is not itself a pointer type or a
                pointer to member type.

            -   Boolean type

                `bool`

            -   Character types

                unsigned char, siggned char, char（不是 signed 就是 unsigned）, char16_t, char32_t, wchar_t。

            -   Integer types

                signed/unsigned short/long, int, long long

                ![](http://whudoc.qiniudn.com/2016/2016-08-09-202600_1366x768_scrot.png)

            -   Floating point types

                float, double, long double

            ![](2016-08-09-202912_1366x768_scrot.png)

        General concepts

        -   Type safety

        Major categories

        -   Static vs. Dynamic
        -   Manifest vs. Inferred
        -   Nominal vs. Structural
        -   Duck typing

        Static and dynamic type checking in practice

        :   The choice between static and dynamic typing requires certain trade-offs.

        "Strong" and "weak" type systems

        :   TODO: <https://en.wikipedia.org/wiki/Strong_and_weak_typing>

        Type safety and memory safety

        :   ```c
            int x = 5;
            char y[] = "37";
            char* z = x + y;
            ```

            As this example shows, C is neither a memory-safe nor a type-safe language.

            In general, type-safety and memory-safety go hand in hand（手拉手，一起走。）.
            For example, a language that supports pointer arithmetic and number-to-pointer
            conversions (like C) is neither memory-safe nor type-safe, because it
            **allows arbitrary memory to be accessed** as if it were valid memory of any type.

            Variable levels of type checking

            -   The `use strict` directive in JavaScript and Perl applies stronger checking.
            -   The `@` operator in PHP suppresses some error messages.
            -   The `Option Strict On` in VB.NET allows the compiler to require a conversion between objects.

        Duck typing

        :   In "duck typing", a statement calling a method `m` on an object does
            not rely on the declared type of the object; only that the object, of
            whatever type, must supply an implementation of the method called, when
            called, at run-time.

            Duck typing differs from **structural typing** in that, if the "part" (of
            the whole module structure) needed for a given local computation is
            present at runtime, the duck type system is satisfied in its type
            identity analysis. On the other hand, a structural type system would
            require the analysis of the whole module structure at compile time to
            determine type identity or type dependence.

            Duck typing differs from a **nominative type** system in a number of
            aspects. The most prominent ones are that for duck typing, type
            information is determined at runtime (as contrasted to compile time),
            and the name of the type is irrelevant to determine type identity or
            type dependence; only partial structure information is required for
            that for a given point in the program execution.

            `['nɑmɪnətɪv]`, 主格

            Duck typing uses the **premise** (`['premɪs]`, 假定) that (referring to
            a value) "**if it walks like a duck, and quacks like a duck, then it is
            a duck**" (this is a reference to the duck test that is attributed to
            James Whitcomb Riley).  The term may have been coined[citation needed]
            by Alex Martelli in a 2000 message to the comp.lang.python newsgroup
            (see Python).

            While one controlled experiment showed an increase in developer
            productivity for duck typing in single developer projects, other
            controlled experiments on API usability show the opposite.

            [Duck typing - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Duck_typing)

            :   In computer programming, duck typing is an application of the duck test
                in type safety. It requires that type checking is deferred to runtime (推迟到运行时),
                and is implemented by means of dynamic typing or reflection.

                Duck typing is concerned with establishing the suitability of an object
                for some purpose. With normal typing, suitability is assumed to be
                determined by an object's type only. In duck typing, an object's
                suitability is determined by the presence of certain methods and
                properties (with appropriate meaning), rather than the actual type of
                the object. The analogy to the duck test appeared in a
                Python Enhancement Proposal discussion in 2002.

-   [Resource Acquisition Is Initialization - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Resource_Acquisition_Is_Initialization) -<

    :   Resource Acquisition Is Initialization (RAII) is a programming idiom used
        in several object-oriented languages, most prominently C++, where it
        originated, but also D, Ada, Vala, and Rust. The technique was developed
        for exception-safe resource management in C++ during 1984–89, primarily by
        Bjarne Stroustrup and Andrew Koenig, and the term itself was coined by
        Stroustrup.  RAII is generally pronounced as an initialism, sometimes
        pronounced as "R, A, double I".

        The following C++11 example demonstrates usage of RAII for file access and mutex locking:

        ```cpp
        #include <mutex>
        #include <iostream>
        #include <string>
        #include <fstream>
        #include <stdexcept>

        void write_to_file (const std::string & message) {
            // mutex to protect file access (shared across threads)
            static std::mutex mutex;    // 只能是静态的，因为 static 才能 lock 一个函数

            // lock mutex before accessing file
            std::lock_guard<std::mutex> lock(mutex);

            // try to open file
            std::ofstream file("example.txt");
            if (!file.is_open())
                throw std::runtime_error("unable to open file");

            // write message to file
            file << message << std::endl;

            // file will be closed 1st when leaving scope (regardless of exception)
            // mutex will be unlocked 2nd (from lock destructor) when leaving
            // scope (regardless of exception)
        }
        ```

-   [Polymorphism (computer science) - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)){.heart} -<

    :   In programming languages and type theory, polymorphism `[,pɑlɪ'mɔrfɪzm]` (from Greek πολύς,
        polys, "many, much" and μορφή, morphē, "form, shape") is the provision of a
        single interface to entities of different types. A polymorphic type is one
        whose operations can also be applied to values of some other type, or
        types.  There are several fundamentally different kinds of polymorphism:

        -   Ad hoc polymorphism

            when a function denotes different and potentially heterogeneous
            implementations depending on a limited range of individually specified
            types and combinations. Ad hoc polymorphism is supported in many
            languages using function overloading.

        -   Parametric polymorphism

            when code is written without mention of any specific type and thus can
            be used transparently with any number of new types. In the
            object-oriented programming community, this is often known as generics
            or generic programming. In the functional programming community, this
            is often shortened to polymorphism.

        -   Subtyping (also called subtype polymorphism or inclusion polymorphism)

            when a name denotes instances of many different classes related by some
            common superclass. In the object-oriented programming community,
            this is often simply referred to as polymorphism.

            **Object-oriented programming languages offer subtype polymorphism using
            subclassing (also known as inheritance).** In typical implementations,
            each class contains what is called a **virtual table**—a table of functions
            that implement the polymorphic part of the class interface—and each
            object contains a pointer to the "vtable" of its class, which is then
            consulted whenever a polymorphic method is called. This mechanism is an
            example of:

            +   **late binding**, because virtual function calls are not bound until
                the time of invocation;
            +   **single dispatch (i.e. single-argument polymorphism)**, because
                virtual function calls are bound simply by looking through the
                vtable provided by the first argument (the this object), so the
                runtime types of the other arguments are completely irrelevant.

        The interaction between parametric polymorphism and subtyping leads to the
        concepts of variance and bounded quantification.

        Static and dynamic polymorphism

        :   Polymorphism can be distinguished by when the implementation is
            selected:

              - **statically (at compile time)** or
              - **dynamically (at run time, typically via a virtual function)**.

            This is known respectively as **static dispatch** and **dynamic dispatch**, and
            the corresponding forms of polymorphism are accordingly called static
            polymorphism and dynamic polymorphism.

            Static polymorphism executes faster, because there is
            **no dynamic dispatch overhead**, but requires additional compiler support.
            Further, static polymorphism allows greater static analysis, by compilers
            (notably for optimization), source code analysis tools, and human
            readers (programmers). Dynamic polymorphism is more flexible but slower
            --for example, dynamic polymorphism allows **duck typing**, and a
            dynamically linked library may operate on objects without knowing their
            full type.

            Static polymorphism typically occurs in ad hoc polymorphism and
            parametric polymorphism, whereas dynamic polymorphism is usual for
            subtype polymorphism. However, it is possible to achieve static
            polymorphism with subtyping through more sophisticated use of template
            metaprogramming, namely **the curiously recurring template pattern**.

-   [Virtual inheritance - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Virtual_inheritance){.heart} -<

    :   Virtual inheritance is a technique used in C++, where a particular base
        class in an **inheritance hierarchy** is declared to share its member data
        instances with any other inclusions of that same base in further derived
        classes. For example, if class A is normally (non-virtually) derived from
        class X (assumed to contain data members), and if class B is also derived
        from class X, and class C inherits from both classes A and B, it will
        contain two sets of the data members associated with class X (accessible
        independently, often with suitable disambiguating qualifiers). But if class
        A is virtually derived from class X instead, then objects of class C will
        contain only one set of the data members from class X. 这个例子像不像是近亲结婚？……

        This feature is most useful for **multiple inheritance**, as it makes the
        virtual base a common subobject for the deriving class and all classes that
        are derived from it. This can be used to avoid the diamond problem by
        clarifying ambiguity over which ancestor class to use, as from the
        perspective of the deriving class (C in the example above) the virtual base
        (X) acts as though it were the direct base class of C, not a class derived
        indirectly through its base (A).

        It is used when inheritance represents restriction of a set rather than
        composition of parts. In C++, a base class intended to be common throughout
        the hierarchy is denoted as virtual with the `virtual` keyword.

        Consider the following class hierarchy.

        ```cpp
        class Animal {
         public:
          virtual void eat();
        };

        class Mammal : public Animal {
         public:
          virtual void breathe();
        };

        class WingedAnimal : public Animal {
         public:
          virtual void flap();
        };

        // A bat is a winged mammal
        class Bat : public Mammal, public WingedAnimal {
        };

        Bat bat;
        ```

        ```cpp
        Bat b;
        Animal &a = b; // error: which Animal subobject should a Bat cast into,
                       // a Mammal::Animal or a WingedAnimal::Animal?
        ```

        To disambiguate, one would have to explicitly convert bat to either base
        class subobject:

        ```cpp
        Bat b;
        Animal &mammal = static_cast<Mammal&> (b);
        Animal &winged = static_cast<WingedAnimal&> (b);

        static_cast<Mammal&>(bat).eat();
        bat.Mammal::eat();
        ```

        ![the diamond problem](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8e/Diamond_inheritance.svg/330px-Diamond_inheritance.svg.png)

        ```cpp
        class Animal {
         public:
          virtual void eat();
        };

        // Two classes virtually inheriting Animal:
        class Mammal : public virtual Animal {
         public:
          virtual void breathe();
        };

        class WingedAnimal : public virtual Animal {
         public:
          virtual void flap();
        };

        // A bat is still a winged mammal
        class Bat : public Mammal, public WingedAnimal {
        };
        ```

        The Animal portion of `Bat::WingedAnimal` is now the same `Animal` instance as
        the one used by `Bat::Mammal`, which is to say that a Bat has only one,
        shared, `Animal` instance in its representation and so a call to `Bat::eat()`
        is unambiguous. Additionally, a direct cast from Bat to Animal is also
        unambiguous, now that there exists only one Animal instance which Bat could
        be converted to.

        The ability to share a single instance of the Animal parent between `Mammal`
        and `WingedAnimal` is enabled by recording the **memory offset** between the
        Mammal or WingedAnimal members and those of the base Animal within the
        derived class. However this offset can in the general case only be known at
        runtime, thus Bat must become (`vpointer`, Mammal, vpointer, WingedAnimal,
        Bat, Animal). There are two vtable pointers, one per inheritance hierarchy
        that virtually inherits Animal. In this example, one for Mammal and one for
        WingedAnimal. The object size has therefore increased by two pointers, but
        now there is only one Animal and no ambiguity. **All objects of type Bat will
        use the same vpointers, but each Bat object will contain its own unique
        Animal object.** If another class inherits from Mammal, such as Squirrel,
        then the vpointer in the Mammal part of Squirrel will generally be
        different to the vpointer in the Mammal part of Bat though they may happen
        to be the same should the Squirrel class be the same size as Bat.

        [Virtual method table - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Vtable)

        :   A virtual method table (VMT), virtual function table, virtual call
            table, dispatch table, vtable, or vftable is a mechanism used in a
            programming language to support dynamic dispatch (or run-time method
            binding).

            Whenever a class defines a virtual function (or method), most compilers
            add a hidden member variable to the class which points to an array of
            pointers to (virtual) functions called the virtual method table (VMT or
            Vtable). These pointers are used at runtime to invoke the appropriate
            function implementations, because at compile time it may not yet be
            known if the base function is to be called or a derived one implemented
            by a class that inherits from the base class.

-   [Run-time type information - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Run-time_type_information) -<

    :   In computer programming, **RTTI** (Run-Time Type Information, or Run-Time Type Identification)
        refers to a C++ mechanism that exposes information about an object's data type
        at runtime. Run-time type information can apply to simple data types,
        such as integers and characters, or to generic types.  This is a C++
        specialization of a more general concept called **type introspection**. Similar
        mechanisms are also known in other programming languages, such as Delphi
        (Object Pascal).

        In the original C++ design, Bjarne Stroustrup did not include run-time type
        information, because he thought this mechanism was frequently misused.

        Any function that gets class information explicitly at runtime:

        -   typeid
        -   dynamic_cast

        Google style 3.26 discourages this, since if you really need it your
        design is probably flawed.

        Also using typeid on variables means that extra meta data must be kept
        about those variables.

        执行期类型识别（Runtime Type Identification RTTI） -<

        :   1.  RTTI 只支持多态类，也就是说没有定义虚函数是的类是不能进行 RTTI的。
            2.  对指针进行dynamic_cast失败会返回NULL ,而对引用的话，识别会抛出 bad_cast exception。
            3.  typeid 可以返回const type_info&，用以获取类型信息。

            关于1是因为RTTI的实现是通过vptr来获取存储在虚函数表中的type_info* ，事实上
            为非多 态类提供RTTI,也没有多大意义。 2的原因在于指针可以被赋值为0，以表示
            no object，但是 引用不行。关于3，虽然第一点指出RTTI只支持多态类，但typeid和
            type_info同样可用于 内建类型及所有非多态类。与多态类的差别在于，非多态类的
            type_info对象是静态取得(所 以不能叫“执行期类型识别”)，而多态类的是在执行期
            获得。

-   [Critical section - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Critical_section)

## 算法数据结构精要

-   [Red–black tree - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Red%E2%80%93black_tree)

## 杂七杂八

-   [4ker/C: C语言](https://github.com/4ker/C)

-   [4ker/Cpp-Primer: C++ Primer 5ed answers](https://github.com/4ker/Cpp-Primer)

-   [sib9/cpp1x-study-resource: 旨在搜集国内外各种 C++11 的学习资源，供大家参考、学习。](https://github.com/sib9/cpp1x-study-resource) -<

    :   ```cpp
        [](int x, int y) { return x + y; } // implicit return type from 'return' statement
        [](int& x) { ++x; }   // no return statement -> lambda function's return type is 'void'
        []() { ++global_x; }  // no parameters, just accessing a global variable
        []{ ++global_x; }     // the same, so () can be omitted


        [](int x, int y) -> int { int z = x + y; return z; }
        ```

        lambda 函数可以使用 lambda 函数外面的标志符。这些变量的集合通常被成为[闭包](https://en.wikipedia.org/wiki/Closure_(computer_science)#Function_objects_.28C.2B.2B.29)，闭包在 lambda 表达式的 `[]` 中定义，允许是值或者引用。如下所示：

        ```
        []        //no variables defined. Attempting to use any external variables in the lambda is an error.
        [x, &y]   //x is captured by value, y is captured by reference
        [&]       //any external variable is implicitly captured by reference if used
        [=]       //any external variable is implicitly captured by value if used
        [&, x]    //x is explicitly captured by value. Other variables will be captured by reference
        [=, &z]   //z is explicitly captured by reference. Other variables will be captured by value
        ```

        下面的两个例子演示了 lambda 表达式的用法：

        ```
        std::vector<int> some_list{ 1, 2, 3, 4, 5 };
        int total = 0;
        std::for_each(begin(some_list), end(some_list), [&total](int x) {
          total += x;
        });
        ```

        计算 `some_list` 的加和，存储到 `total` 中(引用传递)。

        ```
        std::vector<int> some_list{ 1, 2, 3, 4, 5 };
        int total = 0;
        int value = 5;
        std::for_each(begin(some_list), end(some_list), [&, value, this](int x) {
          total += x * value * this->some_func();
        });
        ```

        除 value 和 this 外，引用传递。计算得到 total 的值。只能抓取闭包中的非静态
        函数，lambda 和创建它的时候具有相同的存取权限,不管是否 protected/private 成
        员。

        这篇文章是我从[Move semantics and rvalue references in
        C++11](http://www.cprogramming.com/c++11/rvalue-references-and-move-semantics-in-c++11.html)
        翻译而来的。值得一提的是，不是全文/原文逐字逐句的翻译，加了一些我个人的理解
        ，并进行了一定的精简。

        右值引用会和一个临时对象绑定。比如，在 C++11 之前，如果你有一个临时对象，你
        可以用`regular`或者`lvalue reference` 去绑定它，但是仅仅是在`const`的情况下
        ：

        const string & name = get_name(); // ok
        string& name = get_name(); // NOT ok

        在 C++11 中，右值引用允许你为右值绑定一个可变引用，但是不能是一个左值。换句
        话说，右值引用可以检测到一个对象是不是临时对象。右值引用使用`&&`语法来声明
        而不是`&`，可以是常量，也可以是非常量。和左值引用一样，尽管你很少见到一个常
        量右值引用。

        ```
        const string && name = get_name(); // ok
        string && name = get_name();  // alse ok - praise be!

        void print_ref(const std::string & str)
        {
            std::cout << str << std::endl;
        }

        void print_ref(std::string && str)
        {
            std::cout << str << std::endl;
        }

        std::string name("jerryzhang");
        print_ref(name); // calls the first print_ref function, taking an lvalue reference
        print_ref(get_name()); // calls the second print_ref function, taking a mutable rvalue reference
        ```

-   [The Essence of C++_Bjarne Stroustrup.pptx_微盘下载](http://vdisk.weibo.com/s/G-kaugh7dbcL)

-   [isocpp/CppCoreGuidelines: The C++ Core Guidelines are a set of tried-and-true guidelines, rules, and best practices about coding in C++](https://github.com/isocpp/CppCoreGuidelines){.heart} -<

    :   read: [CppCoreGuidelines/CppCoreGuidelines.md at master · isocpp/CppCoreGuidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md)

        **C++ Core Guidelines**

-   [awesome-c - NotABug.org: Free code hosting](https://notabug.org/koz.ross/awesome-c)

## 精品书摘

-   《深入探索 C++ 对象模型》 -<

    :   第 1 章 关于对象(Object Lessons)` -<

        :   C 语言中数据和操作分开，语言本身没有提供之间的关联性。这种程序方法被称为 Procedural，
            它以一些函数为导向，处理共同的外部数据。

            -   空间布局和存取时间的额外成本 -<

                :   这些额外成本主要由 virtual 引起，包括：

                    -   virtual function 机制，用来支持“执行期绑定”（runtime binding）。
                    -   virtual base class ——虚基类机制，以实现共享虚基类的 subobject。

            -   简单对象模型、表格驱动对象模型以及 C++ 对象模型 -<

                :   C++ 中有两种 class data members：static 和 nonstatic，以及三种
                    class member functions：static、nonstatic、virtual。

                    -   简单对象模型

                        ![](http://www.roading.org/images/2011-11/image_thumb10.png)

                        全部都是指针，全部存起来。每一个实例都要存函数指针，所以空间成本高，
                        执行效率低。

                        C++ 并没有采用这样一种对象模型，但是被用到了C++中“指向成员的指针”（pointer-to-member）的概念当中。

                    -   表格驱动对象模型

                        ![](http://www.roading.org/images/2011-11/image11_thumb1.png)

                        将所有的数据成员抽离出来建成数据成员表，将所有的函 数抽取出来建成一张函
                        数成员表，而对象本身只保持一个指向数据成员表的指针。

                        C++ 也没有采用这一种对象模型，但C++却以此模型作为支持虚函数的方案。

                    -   C++ 对象模型

                        所有的非静态数据成员存储在对象本身中。所有的静态数据成员、成员函数（包
                        括静态与非静态）都置于对象之外。另外，用一张虚函数表（virtual table)存
                        储所有指向虚函数的指针，并在表头附加上一个该类的type_info对象，在对象
                        中则保存一个指向虚函数表的指针。如下图：

                        ![](http://www.roading.org/images/2011-11/image17_thumb1.png)

                        Virtual functions 则以上个步骤支持之：

                        -   每一个 class 产生一堆志向 virtual functions 的指针，放在表
                            格之中。这个表格被称为 **virtual table（vtbl）**。
                        -   每个 class object 被添加一个指针，指向相关的 virtual table。
                            通常这个指针被称为 **vptr**。其 setting 和 resetting 都由每个 class 的
                            constructor 和 destructor 和 copy assignment 运算符自动完成。每个
                            class 所关联的 `type_info object`（用以支持 RTTI，runtime type identification）
                            也经由 virtual table 被指出来，通常是放在表格的第一个 slot 处。

                        这样一来，一个 class X 成员 x 的内存中，有 nonstatic 数据、align 的字节，以及 vptr，
                        这个 vptr 指向一个 virtual table，这个 virtual table 的第一个地
                        址（`px->_vtbl`）指向 type_info for X，其它指向 X 中的各个 virtual function。

            -   三种编程典范 -<

                :   -   程序模型
                    -   ADT 模型
                    -   面向对象模型

            -   一个类的对象（class object）的内存大小包括： -<

                :   -   所有非静态数据成员的大小。
                    -   由内存对齐（alignment）而填补（padding）的内存大小。
                    -   为了支持 virtual 有内部产生的额外负担。

            -   关于面向对象和多态的更多思考 -<

                :   指针的类型，影响的是“**被指出之内存的大小和其内容的解释方式**”。

                    ```
                    Bear b;
                    ZooAnimal *pz = &b;         // pz 之包括其中 ZooAnimal 的 subject
                    Bear *pb = &b;              // pb 指向整个 Bear object

                    pb->cell_block; // okay
                    pz->cell_block; // bad

                    // 不过我们可以 cast
                    ((Bear *)pz)->cell_block;
                    if( Bear *pb2 = dynamic_cast<Bear *>(pz) ) {
                        pb2->cell_block;
                    }
                    ```

                    pz 的类型将在编译时期决定以下两点：

                    -   固定的可用接口（ZooAnimal 的 public 函数接口）
                    -   该接口的 access level

                    ```
                    Bear b;
                    ZooAnimal zb = b;           // 译注：这会引起 sliced
                    zb.rotate();                // 会调用 ZooAnimal::rotate()
                    ```

                    为什么拷贝过去后，vptr 不是指向 `Bear::rotate`？
                    因为编译器在 1）初始化及 2）assignment 操作（即上面的例子）之间
                    作出了仲裁。编译器必须确保如果某个 object 含有一个或一个以上的
                    vptrs，那些 vptrs 的内容不会被 base class object 初始化或改变。

                    初始化或者 assignment 的时候，如 `Derived d; Base b = d;`，derived class object 会被
                    sliced，然后拷贝塞入更小的空间。多态没有出现。如果是引用或者指针，如 `Base *b = &d;` 或者
                    `Base &b = d;` 多态则出现了。

                    >   **C++ 通过 class 的 pointer 和 references 来支持多态，这种程序
                    >   设计风格就称为“面向对象”。**

        第 2 章 构造函数语意学(The Semantics of constructors) -<

        :   深入C++构造函数 -<

            :   -   包含有带默认构造函数的对象成员的类

                    :   若一个类X没有定义任何构造函数，但却包含一个或以上定义有默认构造
                        函数的 对象成员，此时编译器会为X合成默认构造函数，该默认函数会
                        调用对象成员的 默认构造函数为之初始化。如果对象的成员没有定义默
                        认构造函数，那么编译器 合成的默认构造函数将不会为之提供初始化。
                        例如类A包含两个数据成员对象， 分别为：string str和char *Cstr，
                        那么编译器生成的默认构造函数将只提 供对string类型成员的初始化，
                        而不会提供对char*类型的初始化。

                        假如类X的设计者为X定义了默认的构造函数来完成对str的初始化，形如
                        ： A::A(){Cstr=”hello”};因为默认构造函数已经定义，编译器将不能
                        再生成一 个默认构造函数。但是编译器将会扩充程序员定义的默认构造
                        函数——在最前面插 入对初始化str的代码。若有多个定义有默认构造函
                        数的成员对象，那么这些成员 对象的默认构造函数的调用将依据声明顺
                        序排列。

                -   继承自带有默认构造函数的基类的类

                    :   如果一个没有定义任何构造函数的类派生自带有默认构造函数的基类，
                        那么编译 器为它定义的默认构造函数，将按照声明顺序为之依次调用其
                        基类的默认构造函 数。若该类没有定义默认构造函数而定义了多个其他
                        构造函数，那么编译器扩充 它的所有构造函数——加入必要的基类默认构
                        造函数。另外，编译器会将基类的默 认构造函数代码加在对象成员的默
                        认构造函数代码之前。

                -   带有虚函数的类

                    :   带有虚函数的类，与其它类不太一样，因为它多了一个vptr，而vptr的
                        设置是由 编译器完成的，因此编译器会为类的每个构造函数添加代码来
                        完成对vptr的初始 化。

                -   带有一个虚基类的类

                    :   在这种情况下，编译器要将虚基类在类中的位置准备妥当，提供支持虚
                        基类的机 制。也就是说要在所有构造函数中加入实现前述功能的的代码。
                        没有构造函数将 合成以完成上述工作。

                总结：简单来讲编译器会为构造函数做的一点事就是调用其基类或成员对象的默
                认构造函数，以及初始化vprt以及准备虚基类的位置。

                总的来说，编译器将对构造函数动这些手脚：

                -   如果类虚继承自基类，编译器将在所有构造函数中插入准备虚基类位置的代
                    码和提供支持虚基类机制的代码。
                -   如果类声明有虚函数，那么编译器将为之生成虚函数表以存储虚函数地址，
                    并将虚函数指针（vptr）的初始化代码插入到类的所有构造函数中。
                -   如果类的父类有默认构造函数，编译将会对所有的默认构造函数插入调用其
                    父类必要的默认构造函数。必要是指设计者没有显示初始化其父类，调用顺
                    序，依照其继承时声明顺序。
                -   如果类包含带有默认构造函数的对象成员，那么编译器将会为所有的构造函
                    数插入对这些对象成员的默认构造函数进行必要的调用代码，所谓必要是指
                    类设计者设计的构造函数没有对对象成员进行显式初始化。成员对象默认构
                    造函数的调用顺序，依照其声明顺序。
                -   若类没有定义任何构造函数，编译器会为其合成默认构造函数，再执行上述
                    四点。

            拷贝构造函数（copy constuctor） -<

            -   通常C++初级程序员会认为当一个类为没有定义拷贝构造函数的时候，编译器会
                为其合成一个，答案是否定的。编译器只有在必要的时候在合成拷贝构造函数。
                那么编译器什么时候合成，什么时候不合成，合成的拷贝构造函数在不同情况下
                分别如何工作呢？这是本文的重点。

                拷贝构造函数的定义

                :   有一个参数的类型是其类类型的构造函数是为拷贝构造函数。如下：

                    ```cpp
                    X::X( const X& x);
                    Y::Y( const Y& y, int =0 );
                    //可以是多参数形式，但其第二个即后继参数都有一个默认值
                    ```

                拷贝构造函数的应用

                :   当一个类对象以另一个同类实体作为初值时，大部分情况下会调用拷贝构造
                    函数。 一般是这三种具体情况：

                    -   显式地以一个类对象作为另一个类对象的初值，形如X xx=x;
                    -   当类对象被作为参数交给函数时。
                    -   当函数返回一个类对象时。

                编译器何时合成拷贝构造函数

                :   并不是所有未定义有拷贝构造函数的类编译器都会为其合成拷贝构造函数，
                    编译 器只有在必要的时候才会为其合成拷贝构造函数。所谓必要的时刻是指编译
                    器在 普通手段无法完成解决“当一个类对象以另一个同类实体作为初值”时，才会
                    合成 拷贝构造函数。也就是说，当常规武器能解决问题的时候，就没必要动用非
                    常规 武器。

                    如果一个类没有定义拷贝构造函数，通常按照“成员逐一初始化(Default
                    Memberwise Initialization)”的手法来解决“一个类对象以另一个同类实体作为
                    初值”——也就是说把内建或派生的数据成员从某一个对象拷贝到另一个对象身上，
                    如果数据成员是一个对象，则递归使用“成员逐一初始化(Default Memberwise
                    Initialization)”的手法。

                    成员逐一初始化(Default Memberwise Initialization)具体的实现方式则是
                    位 逐次拷贝（Bitwise copy semantics）1。也就是说在能使用这种常规方
                    式 来解决“一个类对象以另一个同类实体作为初值”的时候，编译器是不需要
                    合成拷 贝构造函数的。但有些时候常规武器不那么管用，我们就得祭出非常
                    规武器了 ——拷贝构造函数。有以下几种情况之一，位逐次拷贝将不能胜任或
                    者不适合来完 成“一个类对象以另一个同类实体作为初值”的工作。此时，如
                    果类没有定义拷贝 构造函数，那么编译器将必须为类合成一个拷贝构造函数。

                    -   当类内含一个成员对象，而后者的类声明有一个拷贝构造函数时（不论
                        是设 计者定义的还是编译器合成的）。
                    -   当类继承自一个声明有拷贝构造函数的类时（同样，不论这个拷贝构造
                        函数 是被显示声明还是由编译器合成的）。
                    -   类中声明有虚函数。
                    -   当类的派生串链中包含有一个或多个虚基类。

                    对于前两种情况，不论是基类还是对象成员，既然后者声明有拷贝构造函数
                    时， 就表明其类的设计者或者编译器希望以其声明的拷贝构造函数来完成“
                    一个类对象 以另一个同类实体作为初值”的工作，而设计者或编译器这样做—
                    —声明拷贝构造函 数，总有它们的理由，而通常最直接的原因莫过于因为他
                    们想要做一些额外的工 作或“位逐次拷贝”无法胜任。

                    对于有虚函数的类，如果两个对象的类型相同那么位逐次拷贝其实是可以胜
                    任的。 但问题将出现在，如果基类由其继承类进行初始化时，此时若按照位
                    逐次拷贝来 完成这个工作，那么基类的vptr将指向其继承类的虚函数表，这
                    将导致无法预料 的后果——调用一个错误的虚函数实体是无法避免的，轻则带
                    来程序崩溃，更糟糕 的问题可能是这个错误被隐藏了。所以对于有虚函数的
                    类编译器将会明确的使被 初始化的对象的vptr指向正确的虚函数表。因此有
                    虚函数的类没有声明拷贝构造 函数，编译将为之合成一个，来完成上述工作
                    ，以及初始化各数据成员，声明有 拷贝构造函数的话也会被插入完成上述工
                    作的代码。

                    对于继承串链中有虚基类的情况，问题同样出现在继承类向基类提供初值的
                    情况， 此时位逐次拷贝有可能破坏对象中虚基类子对象的位置。

            命名返回值优化和成员初始化队列 -<

            :   对于一个如foo()这样的函数，它的每一个返回分支都返回相同的对象，编译器
                有可能对其做 **Named return Value 优化（NRV 优化）**，方法是以一个参
                数 result 取代返回对象。

                ```cpp
                X foo()                            void  foo(X &result)
                {                                  {
                    X xx;                              result.X::X();
                    if(...)                            if(...)
                        return xx;                     {
                    else                                   //直接处理result
                        return xx;                         return;
                }                                      }
                                                       else
                                                       {
                                                           //直接处理result
                                                           return;
                                                       }
                                                   }
                ```

                对比优化前与优化后的代码可以看出，对于一句类似于X a = foo()这样的代 码，
                NRV优化后的代码相较于原代码节省了一个临时对象的空间（省略了xx）,同 时
                减少了两次函数调用（减少xx对象的默认构造函数和析构函数，以及一次拷贝 构
                造函数的调用，增加了一次对a的默认构造函数的调用）。

            成员初始化队列（Member Initialization List） -<

            :   对于初始化队列，我相信厘清一个概念是非常重要的：在构造函数中对于对象 成
                员的初始化发生在初始化队列中——或者我们可以把初始化队列直接看做是对 成员
                的定义，而构造函数体中进行的则是赋值操作。所以不难理解有四种情况 必须用
                到初始化列表：

                -   有const成员
                -   有引用类型成员
                -   成员对象没有默认构造函数
                -   基类对象没有默认构造函数

            前两者因为要求定义时初始化，所以必须明确的在初始化队列中给它们提供
            初 值。后两者因为不提供默认构造函数，所有必须显示的调用它们的带参构
            造函 数来定义即初始化它们。

            显而易见的是当类中含有对象成员或者继承自基类的时候，在初始化队列中
            初 始化成员对象和基类子对象会在效率上得到提升——省去了一些赋值操作嘛。

            最后，一个关于初始化队列众所周知的陷阱，初始化队列的顺序，请参考《
            C++ primer》或者《深度探索C++对象模型》。

        第 3 章 Data语意学（The Semantics of Data） :hearts: -<

        :   C++ 类对象的大小 -<

            :   事实上，对于像X这样的一个的空类，编译器会对其动点手脚——隐晦的插入一个字节。为
                什么要这样做呢？插入了这一个字节，那么X的每一个对象都将有一个独一无二的地址。
                如果不插入这一个字节呢？哼哼，那对X的对象取地址的结果是什么？两个不同的X对象间
                地址的比较怎么办？

                对于那位Lippman的法国读者来说，A的 大小是共享的X实体1字节,X和Y的大小分别减去虚
                基类带来的内存空间，都是4。A的总计 大小为9，alignment以后就是12了。而对于vs2010
                来说，那个一字节被优化后，A的大小 为8，也不需再进行alignment操作。


                影响C++类的大小的三个因素：

                -   支持特殊功能所带来的额外负担（对各种virtual的支持）。
                -   编译器对特殊情况的优化处理。
                -   alignment操作，即内存对齐。

                C++ 对象模型尽量以空间优化和存取速度优化考虑来表现 nonstatic data members，
                并且保持和 C 语言 struct 数据配置的兼容性。

                Member rewriting rule

                :   也可以说是 **member scope resolution rules**。
                    这样的好处是，你不必把所有的 data members 放在一开始就声明。
                    唯一的特例是类中的子类型（nested type），需要放在前面。

            VC内存对齐准则（Memory alignment） -<

            :   ```cpp
                // sizeof(T) == 1+(3) + 4 + 8 = 16
                class T {
                    char c;
                    int i;
                    double  d;
                };

                // sizeof(T) == 1+(7) + 8 + 4+(4) = 24
                class T {
                    char c;
                    double  d;
                    int i;
                };
                ```

                Vptr影响对齐而VbcPoint(Virtual base class pointer)不影响

                ```
                class X{char a;};
                class Y: virtual public X{};
                ```

            C++ 对象的数据成员 -<

            :   C++ 标准的规定

                -   在同一个Access Section（也就是private,public,protected片段）中， 要求较晚出
                    现的数据成员处在较大的内存中。这意味着同一个片段中的数据成员并不需要紧密相
                    连，编译器所做的成员对齐就是一个例子。
                -   允许编译器将多个Acess Section的顺序自由排列，而不必在乎它们的声明 次序。但
                    似乎没有编译器这样做。
                -   **对于继承类，C++标准并未指定是其基类成员在前还是自己的成员在前。** （这一点有点反直觉）
                -   对于虚基类成员也是同样的未予规定。

                一般的编译器怎么做？

                -   同一个Access Section中的数据成员按期声明顺序，依次排列。 但成员与成员之间因为内存对齐的原因可能存在空当。
                -   多个Access Section按其声明顺序排放。
                -   **基类的数据成员总放在自己的数据成员之前，但虚基类除外。**（神吗意思？）

                **为了实现虚函数和虚拟继承两个功能，编译器一般会合成 Vptr 和
                Vbptr 两个指针。**那么这两个指针应该放在什么位置？C++ 标准肯定
                是不曾规定的，因为它甚至并没有规定如何来实现这两个功能，因此就
                语言层面来看是不存在这两个指针的。

                对于 Vptr 来说有的编译器将它放在末尾，如Lippman领导开发的Cfront。
                有的则将其放在最前面，如MS的VC，但似乎没人将它放在中间。为什
                么不放在中间？没有理由可以让人这么做，放在末尾，可以保持C++类对
                C的struct的良好兼容性，放在最前可以给多重继承下的指针或引用调用
                虚函数带来好处。

                看一小段代码：

                ```cpp
                #include <iostream>
                #include <stddef.h>

                class X {
                public:
                    int a;
                    virtual void vfc() { }
                };

                int main()
                {
                    X x;
                    std::cout << &x.a<< " " << &x << std::endl;
                    std::cout << "offset: " << offsetof( X, a ) << std::endl;
                }
                ```

                在 VS2010 和 VC6.0 中运行的结果都是地址值 `&x.a` 比 `&x` 大 4，
                可见说 vc 的 vptr 放在对象的最前面此言非虚。（在 GCC 4.8 x64 中是大 8，结论一样。）

                对于Vbptr来说，有好几种方法，在这儿我们只看看VC的实现原理：???

                >   对于由虚拟继承而得的类，VC 会在其每一个对象中插入一个 Vbptr，
                >   这个 Vbptr 指向 vitual base class table（虚基类表）。虚基
                >   类表中则存放有其虚基类子对象相对于虚基类指针的偏移量。例如
                >   声明如 `class Y:virtual public X` 的类的 virtual base class
                >   table 的虚基类表中当存储有 X 对象相对于Vbptr的偏移量。

                对象成员或基类对象成员后面的填充空白不能为其它成员所用，这样才能保证二进制的兼容性。

                ```
                X x;
                X2 x2;

                x2 = x;     // 如果 padding 可以为 drived 类使用，会发生覆盖
                x  = x2;    // 会发生截断，只会 copy sizeof(x) 大小的内存。
                ```

                Vptr 与 Vbptr ???

                -   在多继承情况下，即使是多虚拟继承，继承而得的类只需维护一个Vbptr； 而多继承
                    情况下Vptr则可能有要维护多个Vptr，视其基类有几个有虚函数。
                -   一条继承线路只有一个Vptr，但可能有多个Vbptr，视有几次虚拟 继承而定。换言之，
                    对于一个继承类对象来说，不需要新合成vptr，而是使用其基类子对象的vptr。而
                    对于一个虚拟继承类来说，必须新合成一个自己的Vbptr。

                ```cpp
                #include <iostream>
                #include <stddef.h>
                #include <stdio.h>

                class X {
                    virtual void vf(){}
                    private: double d;
                };

                class X2: virtual public X {
                    virtual void vf(){}
                    private: double d;
                };

                class X3: virtual public X2 {
                    virtual void vf(){}
                    private: double d;
                };

                int main()
                {
                    X x;
                    X2 x2;
                    X3 x3;
                    printf( "sizeof X, X2, X3: %lu, %lu, %lu.\n", sizeof(x), sizeof(x2), sizeof(x3) );
                    // sizeof X, X2, X3: 16, 32, 48.
                }
                ```

                X3将包含有一个Vptr，两个Vbptr。确切的说这两个Vbptr一个属于X3，一个属于X3的子对
                象X2,X3通过其Vbptr找到子对象X2，而X2通过其Vbptr找到X。

                其中差别在于vptr通过一个虚函数表可以确切地知道要调用的函数，而Vbptr通过虚基类表
                只能够知道其虚基类子对象的偏移量。这两条规则是由虚函数与虚拟继承的实现方式，以
                及受它们的存取方式和复制控制的要求决定的。

            数据成员的存取

            :   静态数据成员相当于一个仅对该类可见的全局变量，因为程序中只存在一个静态数据
                成员的实例，所以其地址在编译时就已经被决定。不论如何静态数据成员的存取不会
                带来任何额外负担。

                非静态数据成员的存取，相当于对象起始地址加上偏移量。效率上与C struct成员的
                效率等同。因为它的偏移量在编译阶段已经确定。**但有一种情况例外：`pt->x=0.0`。当
                通过指针或引用来存取——x而x又是虚基类的成员的时候。因为必须要等到执行期才能
                知道pt指向的确切类型，所以必须通过一个间接导引才能完成。**

            在VC中数据成员的布局顺序为：

            -   vptr部分（如果基类有，则继承基类的）
            -   vbptr （如果需要）
            -   基类成员（按声明顺序）
            -   自身数据成员
            -   虚基类数据成员（按声明顺序

            ```cpp
            class Point3d : public Point2d {
            public:
                Point3d( float x = 0.0f, float y = 0.0f, float z = 0.0f )
                  : Point2d( x, y )
                  , _z( z ) { }

                void operator+=( const Point2d &rhs ) {
                    Point2d::operator+=( rhs );
                    _z += rsh.z();
                }

                ...

            protected:
                float _z;
            };
            ```

        第 4 章 Function语意学（The Semantics of Function） -<

        :   C++ 之成员函数调用 -<

            :   c++ 支持三种类型的成员函数，分别为 static, nostatic, virtual。每一种调用方式都不尽相同。

                非静态成员函数（Nonstatic Member Functions） -<

                :   ```cpp
                    // 1. 将成员函数写成一个外部函数：

                        // 成员函数
                        float Point::X();
                        // 外部函数
                        float Point::X( Point *const this );

                        // 或者函数是 const 的，this 指针也要加上 const
                        float Point::X() const;
                        float Point::X( const Point *const this );

                    // 2. 对函数名进行“mangling”处理，使之成为独一无二的名称（略）
                    ```

                    ```cpp
                    // Point3d obj;
                    obj.normalize();
                    ```

                    这里不需要使用 `(*p->vptr)()`，因为编译期间就可以处理好，
                    用 mangled 函数名直接调用即可：`normalize__7Point3dFv( &obj );`。

                    对于静态函数，`&Point3d::object_count();` 会得到一个数值，
                    类型是 `unsigned int (*)();`（函数指针），而不是 `unsigned
                    int (Point3d::*)();`（函数指针，但是是相对的便宜量）

                    ```cpp
                    // method 1
                    float *ax = &pA.x;
                    cout << *ax;

                    // method 2
                    float Point3d::*ax = &Point3d::x;
                    cout << pA.*ax;
                    ```

                虚拟成员函数(Virtual Member Functions) -<

                :   如果function()是一个虚拟函数，那么用指针或引用进行的调用将发生一点特别的转
                    换——一个中间层被引入进来。例如：

                    ```cpp
                    // p->function()
                    //将转化为
                    (*p->vptr)(p);
                    ```

                    -   其中 vptr 为指向虚函数表的指针，它由编译器产生。
                        **vptr 也要进行名字处理，因为一个继承体系可能有多个 vptr。**
                    -   `1` 是虚函数在虚函数表中的索引，通过它关联到虚函数 `function()`.

                    >   在 C++ 中，多态（polymorphism）表示“以一个 public base class“的
                    >   指针（或 reference），寻址出一个 derived class object”的意思。

                静态成员函数(Static Member Functions) -<

                :   静态成员函数的一些特性：

                    -   不能够直接存取其类中的非静态成员（nostatic members），包括不能调用非静
                        态 成员函数(Nonstatic Member Functions)。
                    -   不能够声明为 const、voliatile 或 virtual。
                    -   它不需经由对象调用，当然，通过对象调用也被允许。


            C++ 之虚函数(Virtual Member Functions) -<

            :   **《深度探索C++对象模型》是这样来说多态的:**

                >   在 C++ 中,多态表示“以一个public base class 的指针（或引用），寻址出一
                >   个 derived class object”的意思。

                消极多态与积极多态 -<

                :   用基类指针来寻址继承类的对象，我们可以这样：

                    ```cpp
                    Point *ptr = new Point3d;               //Point3d继承自Point

                    // passive polymorphism，消极多态
                    ptr->x();
                    ```

                    在这种情况下，多态可以**在编译期完成（虚基类情况除外），因此被称作消
                    极多态（没有进行虚函数的调用）**。相对于消极多态，则有积极多态——指向
                    的对象类型需要在执行期在能决定。**积极多态的例子如虚函数和 RTTI**：

                    ~~~ {.cpp .featured}
                    // active polymorphism，积极多态，调用了虚函数
                    ptr->z();

                    // active polymorphism, RTTI 的应用
                    if( Point3d *p =dynamic_cast<Point3d*>(ptr) ) {
                    return p->z();
                    }
                    ~~~

                    识别一个 class 是否支持多态，就是看他有没有任何 virtual function。
                    我们为每个多态的 class object 增加两个 members：

                    -   一个字符串或数字，表示 class 的类型；
                    -   一个指针，指向某表格，表格中带有程序的 virtual functions 的执行期地址。

                单继承下的虚函数 -<

                :   虚函数的实现：

                    -   为每个有虚函数的类配一张虚函数表，它存储该类类型信息和所有虚函数执行期的地址。
                    -   为每个有虚函数的类插入一个指针（vptr）,这个指针指向该类的虚函数表。
                    -   给每一个虚函数指派一个在表中的索引。

                    用这种模型来实现虚函数得益于在C++中,虚函数的地址在编译期是可知的，而且这一地址
                    是固定不变的。而且表的大小不会在执行期增大或减小。

                    一个类的虚函数表中存储有类型信息（存储在索引为0的位置）和所有虚函数地址，这些虚函数地址包括三种：

                    -   这个类定义的虚函数，会改写（overriding）一个可能存在的基类的虚函数实体——假如基类也定义有这个虚函数。
                    -   继承自基类的虚函数实体，——基类定义有，而这个类却没有定义。直接继承之。
                    -   一个纯虚函数实体。用来在虚函数表中占座，有时候也可以当做执行期异常处理函数。

                    每一个虚函数都被指派一个固定的索引值，这个索引值在整个继承体系中保持前后关联，
                    例如,假如z()在Point虚函数表中的索引值为2，那么在Point3d虚函数表中的索引值也为2

                    ```cpp
                    // 基类 Point
                    class Point {
                    public:
                        virtual ~Point();
                        virtual Point& mult( float ) = 0;       // pure virtual function，纯虚函数
                                                                // 在 vtbl 里没有 slot，对应的是 pure_virtual_called()
                        float x() const { return _x; }          // 不是虚函数，不需要在 vtbl 里加入 slot
                        virtual float y() const { return 0; }   // vtbl 中有 slot 指向实现
                        virtual float z() const { return 0; }
                    protected:
                        Point( float x = 0.0 );
                        float _x;
                    };
                    ```

                    ```cpp
                    // Derived Class Point2d
                    class Point2d : public Point {
                    public:
                        Point2d( float x = 0.0f, float y = 0.0f )
                          : Point( x ), _y( y ) { }
                        ~Point2d();

                        // 改写 base class virtual functions
                        Point2d& mult( float );
                        float y() const { return _y; }
                        ...
                    protected:
                        float _y;
                    };
                    ```

                    一共有三种可能：

                    1.  它可以继承 base class 所声明的 virtual function（其实就是 vtbl
                        下相应 slot（下的函数指针）拷贝过去）；
                    2.  使用自己的函数实体，应该放到相应的位置 slot，覆盖原来的函数指针；
                    3.  添加新的 virtual function，这时候 virtual table 会变大，新的
                        slot 会被 append 到后方。

                    ```cpp
                    class Point3d : public Point2d {
                    public:
                        Point3d( float x = 0.0f, float y = 0.0f, float z = 0.0f )
                          : Point2d( x, y ), _z( z ) { }
                        ~Point3d();

                        // 改写 base class 的 virtual functions
                        Point3d& mult( float );
                        float z() const { return _z; }
                        ...
                    protected:
                        float _z;
                    };
                    ```

                    `ptr->z()` 如何在编译时期设定 virtual function 的调用呢？

                    1.  我们不知道 ptr 所指的类型，但我们可以拿到该对象的 vtbl；
                    2.  我们不知道哪个 z() 要被调用，但 z() 对应的 slot 都是一个位置（比如 slot4）

                    于是编译器就可以把 `ptr->z()` 转化为 `(*ptr->vptr)( ptr )`。
                    这里 vptr 和 4 都是在编译期间就知道的，只有 ptr 的地址和具体调用哪
                    个 z() 是运行期间才知道的。

                    pure virtual functions？

                    :   ???

                多重继承下的虚函数 -<

                :   ```cpp
                    Base2 *ptr = new Derived;

                    // 需要被转换为，这个转换在编译期完成
                    Derived *temp = new Derived;
                    Base2 *ptr = temp ? temp + sizeof(base1) : 0 ; // 偏移“掉” Base1 的那部分
                    ```

                    当要delete ptr时又面临了一次转换，因为在delete ptr的时候，需要对整
                    个对象而不是其子对象施行delete运算符，这期间需要调整ptr指向完整的对
                    象起点，因为不论是调用正确的析构函数还是delete运算符都需要一个指向
                    对象起点的指针，想一想给予一个derived类的成员函数指向base2
                    subobjuect 的this指针会发生什么吧。因为ptr的具体类型并不知道，所以
                    必须要等到执行期来完成。

                    Bjame 的解决方法是将每一个虚函数表的 slot 扩展，以使之存放一个额外的
                    偏移量。于是虚函数的调用：

                    ```cpp
                    (*ptr->vptr)(ptr);
                    //将变成：
                    (*ptr->vptr.addr)(ptr+*ptr->vptr.offset);
                    ```

                    其中使用 `ptr->vptr.addr` 用以获取正确的虚函数地址，而
                    `ptr+*ptr->vptr.offset` 来获得指向对象完整的起点。这种方法的缺点显
                    而易见，代价过大了一点，所有的情况都被这一种占比较小的情况拖累。

                    还有一种叫做thunk的方法，thunk的作用在于:

                    -   以适当的offset值来this调整指针.
                    -   跳到虚函数中去。

                    多继承下的虚函数，影响到虚函数的调用的实际质上为this的调整。而this调整一般为两种：

                    -   调整指针指向对应的subobject，一般发生在继承类类型指针向基类类型
                        指针赋值的情况下。
                    -   将指向subobject的指针调整回继承类对象的起始点，一般发生在基类指
                        针对继承类虚函数进行调用的时候。

                    还有别忘了，vptr也可以看做一个数据成员，要找到虚函数，前提是获取正确的vptr偏移量。

                虚拟继承下的虚函数 -<

                :   Lippman说，如果一个虚基类派生自另一虚基类，而且它们都支持虚函数和非
                    静态数据成员的时候，编译器对虚基类的支持就像迷宫一样复杂。其实我原
                    想告诉他，我是怀着一颗勇士之心而来的 :你说呢:？。

        第 5 章 构造、解构、拷贝 语意学（Semantics of Construction，Destruction，and Copy） -<

        :   几点类设计原则 -<

            :   一些建议。

            构造、复制、析构语意学

            :   。。。

        第 6 章 执行期语意学（Runtime Semantics）

        第 7 章 站在对象模型的类端（On the Cusp of the Object Model）

        refs and see also

        -   [《深度探索C++对象模型》笔记汇总](http://www.roading.org/develop/cpp/%e3%80%8a%e6%b7%b1%e5%ba%a6%e6%8e%a2%e7%b4%a2c%e5%af%b9%e8%b1%a1%e6%a8%a1%e5%9e%8b%e3%80%8b%e7%ac%94%e8%ae%b0%e6%b1%87%e6%80%bb.html)
        -   [C++之虚函数(Virtual Member Functions)](http://www.roading.org/develop/cpp/c%E4%B9%8B%E8%99%9A%E5%87%BD%E6%95%B0virtual-member-functions.html)

-   CSAPP -<

    :   重点推荐第3章“程序的机器级表示”、第5章“优化程序性能”、第6章“存储器层次
        结构”、第10章“虚拟存储器”。觉得这四章乃是全书之精华，看得人欲罢不能。

-   [《C语言接口与实现:创建可重用软件的技术》 David R. Hanson, 郭旭【摘要 书评 试读】图书](https://www.amazon.cn/gp/product/B005LAJ9F6/ref=as_li_ss_tl?ie=UTF8&camp=536&creative=3132&creativeASIN=B005LAJ9F6&linkCode=as2&tag=lucida-23)

-   [Adoo's blog - Introduction to Algorithm -third edition](http://www.roading.org/category/introduction-to-algorithm-third-edition.html)

-   《DPV -- Algorithm》 -<

    :   [算法之美（Algorithms）书评](https://book.douban.com/review/1325850/)

        :   算法作为一门学问，有两条正交的线索。一个是算法处理的对象：数、矩阵、集
            合、串(strings)、排列(permutations)、图(graphs)、表达式(formula)、分布
            (distributions)，等等。另一个是算法的设计思想：贪婪、分治、动态规划、线
            性规划、局部搜索(local search)，等等。这两条线索几乎是相互独立的：同一
            个离散对象，例如图，稍有不同的问题，例如single-source shortest path
            和all-pair shortest path，就可以用到不同的设计思想，如贪婪和动态规
            划；而完全不同的离散对象上的问题，例如排序和整数乘法，也许就会用到
            相同的思想，例如分治。

-   《The Algorithm Design Manual》 -<

    :   [Skiena's Audio Lectures](http://www3.cs.stonybrook.edu/~algorith/video-lectures/)

        [不愧对“手册”之名，即使通读过CLRS再读也有所收获（算法设计手册）书评](https://book.douban.com/review/6250350/)

## Blog Posts

-   [Sorting algorithm - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Sorting_algorithm)

-   [E.W.Dijkstra Archive: The Humble Programmer (EWD 340)](https://www.cs.utexas.edu/~EWD/transcriptions/EWD03xx/EWD340.html)

-   [Smart pointer - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Smart_pointer) -<

    :   In computer science, a smart pointer is an abstract data type that
        **simulates a pointer while providing added features, such as automatic
        memory management or bounds checking**. Such features are intended to
        reduce bugs caused by the misuse of pointers, while retaining
        efficiency. Smart pointers typically keep track of the memory they
        point to, and may also be used to manage other resources, such as
        network connections and file handles. Smart pointers originated in the
        programming language C++.

        从较浅的层面看，智能指针是利用了一种叫做 RAII（资源获取即初始化）的技术
        对普通的指针进行封装，这使得智能指针实质是一个对象，行为表现的却像一个
        指针。作用当然很明显，**防止忘记调用 delete**，当然还有另一个作用， @胡昊 也
        指出来了，就是**异常安全**。在一段进行了try/catch的代码段里面，即使你写入了
        delete，也有可能因为发生异常，程序进入 catch 块，从而忘记释放内存，这些都
        可以通过智能指针解决。

        但是智能指针还有一重更加深刻的含义，就是把 @陈硕所说的
        **value语义**转化为 **reference语义**。

        ```cpp
        std::shared_ptr<some_type>
        auto s = std::make_shared<some_type>(constructor, parameters, here);

        std::unique_ptr<some_type>
        auto u = std::make_unique<some_type>(constructor, parameters, here);
        ```

        `unique_ptr` -<

        :   ```cpp
            std::unique_ptr<int> p1(new int(5));

            // Compile error.
            std::unique_ptr<int> p2 = p1;

            // Transfers ownership. p3 now owns the memory and p1 is rendered invalid.
            std::unique_ptr<int> p3 = std::move(p1);

            p3.reset(); // Deletes the memory.
            p1.reset(); // Does nothing.
            ```

        `shared_ptr` -<

        :   ```cpp
            std::shared_ptr<int> p1(new int(5));
            std::shared_ptr<int> p2 = p1; // Both now own the memory.

            p1.reset(); // Memory still exists, due to p2.
            p2.reset(); // Deletes the memory, since no one else owns the memory.
            ```

            `shared_ptr` 中所实现的本质是**引用计数(reference counting)**，也就
            是说 `shared_ptr` 是支持复制的，复制一个 `shared_ptr` 的本质是对这个
            智能指针的引用次数加 1，而当这个智能指针的引用次数降低到 0 的时候，
            该对象自动被析构。

            需要特别指出的是，如果 `shared_ptr` 所表征的引用关系中出现一个环(???例子？)，那么
            环上所述对象的引用次数都肯定不可能减为 0 那么也就不会被删除，为了解决
            这个问题引入了 weak_ptr。

        `weak_ptr` -<

        :   对 weak_ptr 起的作用，很多人有自己不同的理解，我理解的 weak_ptr 和
            shared_ptr 的最大区别在于weak_ptr在指向一个对象的时候不会增加其引用
            计数，因此你可以用weak_ptr去指向一个对象并且在weak_ptr仍然指向这个
            对象的时候析构它，此时你再访问weak_ptr的时候，weak_ptr其实返回的会
            是一个空的shared_ptr。

            ```cpp
            std::shared_ptr<int> p1(new int(5));
            std::weak_ptr<int> wp1 = p1; //p1 owns the memory.

            {
                std::shared_ptr<int> p2 = wp1.lock(); // Now p1 and p2 own the memory.
                if(p2) // As p2 is initialized from a weak pointer, you have to check if the memory still exists!
                {
                    //Do something with p2
                }
            } //p2 is destroyed. Memory is owned by p1.

            p1.reset(); // Memory is deleted.

            std::shared_ptr<int> p3 = wp1.lock(); //Memory is gone, so we get an empty shared_ptr.
            if(p3)
            {
                //Will not execute this.
            }
            ```

            实际上，通常shared_ptr内部实现的时候维护的就不是一个引用计数，而是
            两个引用计数，一个表示 **strong reference**，也就是用 shared_ptr 进行复制
            的时候进行的计数，一个是 **weak reference**，也就是用 weak_ptr 进行复制的
            时候的计数。weak_ptr本身并不会增加strong reference的值，而strong
            reference降低到0，对象被自动析构。

            为什么要采取weak_ptr来解决刚才所述的环状引用的问题呢？需要注意的是
            环状引用的本质矛盾是不能通过任何程序设计语言的方式来打破的，为了解
            决环状引用，第一步首先得打破环，也就是得**告诉C++，这个环上哪一个引用
            是最弱的，是可以被打破的，因此在一个环上只要把原来的某一个
            shared_ptr改成weak_ptr，实质上这个环就可以被打破了，原有的环状引用
            带来的无法析构的问题也就随之得到了解决**。(???)

        `auto_ptr` -<

        :   ```cpp
            #include <iostream>
            #include <memory>
            using namespace std;

            int main(int argc, char **argv)
            {
                int *i = new int;
                auto_ptr<int> x(i);
                auto_ptr<int> y;

                y = x;

                cout << x.get() << endl; // Print NULL
                cout << y.get() << endl; // Print non-NULL address i

                return 0;
            }
            ```

            output:

            ```
            0
            0x1540010
            ```

            [語言技術：C++ Gossip: `auto_ptr`](http://openhome.cc/Gossip/CppGossip/) -<

            :   ```cpp
                #include <memory>
                ```

                auto_ptr可以指向一個以new建立的物件，當auto_ptr的生命週期結束後，所指向
                的物件之資源也會被釋放，在建立auto_ptr時必須指 定目標物件之型態。
                操作auto_ptr就像操作沒有使用auto_ptr的指標一樣。

                ```
                auto_ptr<int> iPtr (new int(100));
                auto_ptr<string> sPtr (new string("caterpillar"));

                cout << *iPtr << endl; // 顯示100
                if(sPtr->empty())
                    cout << "字串為空" << endl;
                ```

                您也可以建立一個未指向任何物件的auto_ptr，例如：`auto_ptr<int> iPtr;`

                未指向任何物件的auto_ptr不可以取值，否則會發生不可預期之結果，既然不可取值，如
                何判斷它是否有指向物件呢？您可以使用get()函式，它會傳 回所指向物件的位址，如果
                傳回0，表示不指向任何物件，如果不指向任何物件，您可以使用reset()來讓它指向一個
                物件，例如：

                ```
                if(iPtr.get() == 0) {
                    iPtr.reset(new int(100));
                }
                ```

                reset()可以接受一個指標或是0表示不指向任何物件，reset()會先delete目前指向的
                物件，然後重新指向新的物件，您也可以使用 release()釋放auto_ptr管理所指向物
                件的職責。

                auto_ptr可以使用另一個auto_ptr來建立，這會造成所有權的轉移，例如：

                ```
                auto_ptr<SafeArray> ptr1(new SafeArray(19));
                auto_ptr<SafeArray> ptr2(ptr1);
                ```

                當使用ptr1來建立ptr2時，ptr1不再對所指向物件的資源釋放負責，職責交給了ptr2
                ，在使用指定運算時，也有類似的行為，例如：

                ```
                auto_ptr<SafeArray> ptr1(new SafeArray(19));
                auto_ptr<SafeArray> ptr2(new SafeArray(20));
                ptr2 = ptr1;
                ```

                ptr2所指向的物件會先被delete，然後ptr1的屬性會複製至ptr2，也就是ptr1所指向
                的物件，現在由ptr2指向它了，ptr1不再負責 所指向物件的資源釋放。

                auto_ptr的資源維護動作是以inline的方式來完成，也就是在編譯時會被擴展開
                來，所以使用auto_ptr並不會犧牲效率。

                最後要注意的是，auto_ptr不能用來管理動態配置而來的陣列，如果用它來管理
                動態配置而來的陣列，結果是不可預期的。

        `scoped_ptr` -<

        :   这是比较简单的一种智能指针，正如其名字所述，scoped_ptr 所指向的对象
            在作用域之外会自动得到析构，一个例子是：

            ```cpp
            #include <boost/scoped_ptr.hpp>
            #include <iostream>

            struct Shoe { ~Shoe() { std::cout << "Buckle my shoe\n"; } };

            class MyClass {
                boost::scoped_ptr<int> ptr;
              public:
                MyClass() : ptr(new int) { *ptr = 0; }
                int add_one() { return ++*ptr; }
            };

            int main()
            {
                boost::scoped_ptr<Shoe> x(new Shoe);
                MyClass my_instance;
                std::cout << my_instance.add_one() << '\n';
                std::cout << my_instance.add_one() << '\n';
            }
            ```

            output:

            ```
            1
            2
            Buckle my shoe
            ```

            此外，scoped_ptr是non-copyable的，也就是说你不能去尝试复制一个
            scoped_ptr的内容到另外一个scoped_ptr中，这也是为了防止错误的多次析
            构同一个指针所指向的对象。

        refs and see also

        -   [c++ - Example to use shared_ptr? - Stack Overflow](http://stackoverflow.com/questions/3476938/example-to-use-shared-ptr)
        -   [scoped_ptr - 1.50.0](http://www.boost.org/doc/libs/1_50_0/libs/smart_ptr/scoped_ptr.htm)
        -   [Smart Pointers - 1.50.0](http://www.boost.org/doc/libs/1_50_0/libs/smart_ptr/smart_ptr.htm)

-   [chenshuo/documents](https://github.com/chenshuo/documents/){.heart} -<

    :   1）慎用匿名 namespace -<

        :   C 语言的 static 关键字有两种用途:

            -   1. 用于函数内部修饰变量,即**函数内的静态变量**。这种变量的生存期长
                于该函数,使得函数具有一定的“状态”。使用静态变量的函数一般是不可
                重入的,也不是线程安全的
            -   2. 用在文件级别(函数体之外)，修饰变量或函数,表示该变量或函数只
                在本文件可见,其他文件看不到也访问不到该变量或函数。专业的说法叫
                “**具有 internal linkage**”(简言之:不暴露给别的 translation unit)。

            C++?

            -   3. 用于**修饰 class 的数据成员,即所谓“静态成员”**。class varible vs
                instance variable
            -   4. 用于**修饰 class 的成员函数,即所谓“静态成员函数”**。这种成员函数
                只能访问 class variable 和其他静态程序函数,不能访问 instance
                variable 或 instance method。

            在 C++ 里不必使用文件级的 static 关键字,我们可以用匿名 namespace 达到相同的效果。

            但匿名 namespace 的不好在于不利于 debug，因为 namespace 名字每次都要变，变量名就变了。

        2）不要重载全局 `::operator new()` -<

        :   如果只考虑分配和释放,内存管理基本要求是“不重不漏”:既不重复 delete,
            也不漏掉 delete。也就说我们常说的 new/delete 要配对,“配对”不仅是个
            数相等,还隐含了 new 和 delete 的调用本身要匹配,不要“东家借的东西西
            家还”

            -   malloc -> free
            -   new -> delete
            -   new[] -> delete[]
            -   ::operator new() -> ::operator delete()

            这可以归结为最小惊讶原则:如果我在代码里读到 Node* p = new Node,我会
            认为它在 heap 上分配了内存,如果 Node class 重载了 member ::operator new(),
            那么我要事先仔细阅读 node.h 才能发现其实这行代码使用了私有的内存池。为什
            么不写得明确一点呢?写成 **`Node* p = NodeFactory::createNode()`{.cpp}**,那么我能猜到
            NodeFactory::createNode() 肯定做了什么与 new Node 不一样的事情,免得将来大
            吃一惊。

            The Zen of Python 说 explicit is better than implicit,我深信不疑。

        -   一个 300 行的 memory buffer output stream -<

            :   <https://github.com/chenshuo/recipes/blob/master/logging>

                [Here be dragons: advances in problems you didn’t even know you had | teideal glic deisbhéalach](http://www.serpentine.com/blog/2011/06/29/here-be-dragons-advances-in-problems-you-didnt-even-know-you-had/)

                ```cpp
                class Fmt : boost::noncopyable
                {
                    public:
                        template<typename T>
                        Fmt(const char* fmt, T val)
                        {
                            BOOST_STATIC_ASSERT(boost::is_arithmetic<T>::value == true);
                            length_ = snprintf(buf_, sizeof buf_, fmt, val);
                        }
                        const char* data() const { return buf_; }
                        int length() const { return length_; }
                    private:
                        char buf_;
                        int length_;
                };

                inline LogStream& operator<<(LogStream& os, const Fmt& fmt)
                {
                    os.append(fmt.data(), fmt.length());
                    return s;
                }

                LogStream os;
                double x = 19.82;
                int y = 43;
                os << Fmt(”%8.3f”, x) << Fmt(”%4d”, y);
                ```

        12）值语义与数据抽象 -<

        :   semantics??? or semantics

            -   什么是值语义-<

                :   **值语义 (value semantics)** 指的是对象的拷贝与原对象无关,就像拷贝
                    int 一样。C++ 的内置类型 (bool/int/double/char) 都是值语义,标准
                    库里的 complex<> 、pair<>、vector<>、map<>、string 等等类型也都
                    是值语意,**拷贝之后就与原对象脱离关系**。

                    [维基](https://en.m.wikipedia.org/wiki/Value_semantics) 上这么介绍的：

                    >   In computer science, having value semantics (also
                    >   value-type semantics or copy-by-value semantics) means
                    >   for an object that only its value counts, not its
                    >   identity. If the concept is fully applied, value
                    >   semantics implies immutability of the object.
                    >
                    >   The concepts that are used to explain this concept are
                    >   extensionality, definiteness, substitutivity of
                    >   identity, unfoldability, and referential transparency.

                    与值语义对应的是“**对象语义/object semantics**”,或者叫做引用语义
                    (reference semantics),由于“引用”一词在 C++ 里有特殊含义,所以我在
                    本文中使用“对象语义”这个术语。对象语义指的是面向对象意义下的对
                    象,【对象拷贝是禁止的】。例如 muduo 里的 Thread 是对象语义,拷贝
                    Thread 是无意义的,也是被禁止的:因为Thread 代表线程,拷贝一个
                    Thread 对象并不能让系统增加一个一模一样的线程。

                    一些辨析：

                    -   值语义与 immutable 无关。C++ 中的值语义对象也可以是 mutable,比
                        如 complex<>、pair<>、vector<>、map<>、string 都是可以修改的。

                    -   值语义的对象不一定是 POD,例如 string 就不是 POD,但它是值语义的。

                    -   值语义的对象不一定小,例如 vector<int> 的元素可多可少,但它始终是
                        值语义的。当然,很多值语义的对象都是小的,例如 complex<>、
                        muduo::Date、muduo:: Timestamp。

            -   值语义与生命期 -<

                :   值语义的一个巨大好处是生命期管理很简单,就跟 int 一样——你不需要操心 int
                    的生命期。值语义的对象要么是 stack object,或者直接作为其他 object 的成员,因
                    此我们不用担心它的生命期(一个函数使用自己 stack 上的对象,一个成员函数使用
                    自己的数据成员对象)。相反,**对象语义的 object 由于不能拷贝,我们只能通过指针
                    或引用来使用它。**

                    一旦使用指针和引用来操作对象,**那么就要担心所指的对象是否已被释放**,这一
                    度是 C++ 程序 bug 的一大来源。此外,由于 C++ 只能通过指针或引用来获得多态
                    性,那么在 C++ 里从事基于继承和多态的面向对象编程有其本质的困难——
                    【对象生命期管理(资源管理)】。

                    我们可以借助 smart pointer 把对象语义转换为值语义, 从而
                    轻松解决对象生命期:让 Parent 持有 Child 的 smart
                    pointer,同时让 Child 持有 Parent 的smart pointer,这样始
                    终引用对方的时候就不用担心出现空悬指针。当然,其中一个
                    smart pointer 应该是 weak reference,否则会出现循环引用,
                    导致内存泄漏。到底哪一个是 weak reference,则取决于具体
                    应用场景。

            -   值语义与标准库 -<

                :   在现代 C++ 中,一般不需要自己编写 copy constructor 或 assignment operator,
                    因为只要每个数据成员都具有值语义的话,编译器自动生成的
                    member-wise copying&assigning 就能正常工作;如果以
                    smart ptr_为成员来持有其他对象,那么就能自动启用或禁用
                    copying&assigning。

            -   值语义与 C++ 语言 :hearts: -<

                :   C++ 的 class 本质上是值语义的,这才会出现 object slicing
                    这种语言独有的问题,也才会需要程序员注意 pass-by-value
                    和 pass-by-const-reference 的取舍。在其他面向对象编程语
                    言中,这都不需要费脑筋。

                    值语义是 C++ 语言的三大约束之一,C++ 的设计初衷是让用户
                    定义的类型(class) 能像内置类型 (int) 一样工作,具有同等
                    的地位。为此 C++ 做了以下设计(妥协):

                    -   class 的 layout 与 C struct 一样,没有额外的开销。定
                        义一个“只包含一个 int 成员的 class ”的对象开销和定
                        义一个 int 一样。

                    -   甚至 **class data member 都默认是 uninitialized**,因为
                        函数局部的 int 是 uninitialized。（这一点其实很反直觉，至少对我而言。）

                    -   class 可 以 在 stack 上 创 建,也 可 以 在 heap 上
                        创 建。因 为 int 可 以 是 stack variable。

                    -   class 的数组就是一个个 class 对象挨着,没有额外的
                        indirection。因为 int 数组就是这样。

                    -   编译器会为 class 默认生成 copy constructor 和
                        assignment operator。其他语言没有 copy constructor
                        一说,也不允许重载 assignment operator。C++ 的对象默
                        认是可以拷贝的,这是一个尴尬的特性。

                    -   当 class type 传入函数时,默认是 make a copy (除非参
                        数声明为 reference)。

                        因为把 int 传入函数时是 make a copy。C++ 的“函数调
                        用”比其他语言复杂之处在于参数传递和返回值传递。C、
                        Java等语言都是传值,简单地复制几个字节的内存就行了。
                        但是 C++ 对象是值语义,如果以 pass-by-value 方式把对
                        象传入函数,会涉及拷贝构造。代码里看到一句简单的函数
                        调用,实际背后发生的可能是一长串对象构造操作,因此减
                        少无谓的临时对象是 C++ 代码优化的关键之一。

                    -   当函数返回一个 class type 时,只能通过 make a
                        copy(C++ 不得不定义 RVO来解决性能问题)。

                        因为函数返回 int 时是 make a copy。

                    -   以 class type 为 成 员 时,数 据 成 员 是 嵌 入 的。
                        例 如 pair<complex<double>, size_t> 的 layout 就是
                        complex<double> 挨着 size_t。

                    **这些设计带来了性能上的好处,原因是 memory locality。**

            -   什么是数据抽象 -<

                :   C++ 的强大之处在于“抽象”不以性能损失为代价。

                    数据抽象 (data abstraction) 是与面向对象
                    (object-oriented) 并列的一种编程范式(programming
                    paradigm)。说“数据抽象”或许显得陌生,它的另外一个名字“抽
                    象数据类型/abstract data type/ADT”想必如雷贯耳。

                    三个常见编程範式是：

                    -   procedural, 程序模型
                    -   ADT 模型, data abstraction
                    -   object-based
                    -   object-oriented, 面向对象模型

                    C++ is a general-purpose programming language with a
                    bias towards systems programming that

                    -   is a better C,
                    -   supports data abstraction, （支持 ADT）
                    -   supports object-oriented programming（支持封装、继承、多态）, and
                    -   supports generic programming.

                    那么到底什么是数据抽象?

                    :   简单的说,数据抽象是用来描述 (抽象) 数据结构的。

                        数据抽象就是 ADT。一个 ADT 主要表现为它**支持的一些操
                        作**,比方说 stack.push、stack.pop,这些操作应该具有明
                        确的时间和空间复杂度。另外,一个 ADT 可以**隐藏其实现细节,
                        比方说 stack 既可以用动态数组实现,又可以用链表实现。

                    按照这个定义,数据抽象和基于对象 (object-based) 很像,那
                    么它们的区别在哪里?语义不同。**ADT 通常是值语义,而
                    object-based 是对象语义。**(这两种语义的定义见前一节《什
                    么是值语义》12.1)。ADT class 是可以拷贝的,拷贝之后的
                    instance 与原 instance 脱离关系。比方说

                    ```cpp
                    stack<int> a;
                    a.push(10);
                    stack<int> b = a;
                    b.pop();
                    ```

                    这时候 a 里仍然有元素 10。

                    本文把 data abstraction、object-based、object-oriented
                    视为三个编程范式。这种细致的分类或许有助于理解区分它们
                    之间的差别。庸俗地讲,面向对象 (object-oriented) 有三大
                    特征:封装、继承、多态。而基于对象 (object-based) 则只有
                    封装,没有继承和多态,即只有具体类,没有抽象接口。它们两个
                    都是对象语义。

                    面向对象真正核心的思想是消息传递 (messaging)，“封装继承多态”只是表象。

                    :   这一点孟岩 63 和王益 64 都有精彩的论述,陈硕不再赘言。

                        refs and see also

                        -   [function/bind的救赎（上） - 孟岩 - 博客频道 - CSDN.NET](http://blog.csdn.net/myan/article/details/5928531)
                        -   [杂谈现代高级编程语言 | Yi Wang's Tech Notes](https://cxwangyi.wordpress.com/2011/06/19/%E6%9D%82%E8%B0%88%E7%8E%B0%E4%BB%A3%E9%AB%98%E7%BA%A7%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80/)

                    数据抽象与它们两个的界限在于“语义”,数据抽象不是对象语义,而是值语

            -   数据抽象所需的语言设施 -<

                :   不是每个语言都支持数据抽象,下面简要列出“数据抽象”所需的语言设施。

                    -   **支持数据聚合**

                        数 据 聚 合 data aggregation, 或 者 value
                        aggregates。 即 定 义 C- style struct,把有关数据放到同
                        一个 struct 里。

                    -   **全局函数与重载**

                        C 语言可以定义全局函数,但是不能与已有的函数重名,也就没
                        有重载。Java 没有全局函数,而且 Math class 是封闭的,并不
                        能往其中添加 sin(Complex)。

                    -   **成员函数与 private 数据**

                    -   **拷贝控制 (copy control)**

                        copy control 是拷贝 `stack a; stack b = a;` 和赋值 `stack b; b = a;` 的合称。

                        由于 C++ class 是值语义,copy control 是实现深拷贝的必要
                        手段。而且 ADT 用到的资源只涉及动态分配的内存,所以深拷
                        贝是可行的。相反,object-based 编程风格中的 class 往往代
                        表某样真实的事物(Employee、Account、File 等等),深拷贝无
                        意义。

                        C 语言没有 copy control,也没有办法防止拷贝,一切要靠程序
                        员自己小心在意。FILE* 可以随意拷贝,但是只要关闭其中一个
                        copy,其他 copies 也都失效了,跟空悬指针一般。整个 C 语言
                        对待资源(malloc 得到的内存,open() 打开的文件, socket()
                        打开的连接)都是这样,用整数或指针来代表(即“句柄”)。而整
                        数和指针类型的“句柄”是可以随意拷贝的,很容易就造成重复释
                        放、遗漏释放、使用已经释放的资源等等常见错误。这方面
                        C++ 是一个显著的进步,boost::noncopyable 是boost 里最值
                        得推广的库。

                    -   操作符重载

                        如果要写动态数组,我们希望能像使用内置数组一样使用它,比
                        如支持下标操作。C++ 可以重载 operator[] 来做到这一点。

                    -   效率无损

                    -   模板与泛型

                    -   模板与泛型

            >   数据抽象是 C++ 的重要抽象手段,适合封装“数据”,它的语义简单,容易
            >   使用。数据抽象能简化代码书写,减少偶然错误。

        13）再探 std::string -<

        :   std::string 有多种实现方式, 归纳起来有三类：

            -   无特殊处理 (eager copy)

                实现上，通常是：

                -   三个指针：start, finish, end_of_storage
                -   一个指针，两个大小：start, size, capacity

                    如果 size 用 int 表示，那么字符串容量在 2^^32^^-1 bytes =
                    2^^32-10-10-10^^ giga bytes。通常用不着，所以把他换成 short 可以节约空间。

            -   Copy-on-Write (COW)，gcc

                COW 对多线程不友好

                ```cpp
                class cow_string // libstdc++-v3
                {
                    struct Rep
                    {
                        size_t size;
                        size_t capacity;
                        size_t refcount;
                        char* data; // variable length
                    };
                    char* start;
                };
                ```

                这种数据结构没啥好说的,在 64-bit 中似乎也没有优化空间。另外 COW
                的操作复杂度不一定符合直觉,它拷贝字符串是 O(1) 时间,但是拷贝之
                后的第一次operator[] 有可能是 O(N ) 时间。

                refs and see also

                -   http://coolshell.cn/articles/1443.html

            -   短字符串优化 (SSO),利用 string 对象本身的空间来存储短字符串，vc++

            C++03/98 标准没有规定 string 中的字符是连续存储的,但是《Generic
            Programming and the STL》 的 作 者 Matthew Austern 指 出78 :现在所
            有的std::string 实现都是连续存储的,因此建议在新标准中明确规定下来。

        14）用 STL algorithm 秒杀几道算法面试题 -<

        :   生成 N 个不同元素全排列 -<

            :   ```cpp
                #include <algorithm>
                #include <iostream>
                #include <iterator>
                #include <vector>

                int main()
                {
                  // 这里的顺序须是字典顺序
                  int elements[] = { 1, 2, 3, 4 };
                  const size_t N = sizeof(elements)/sizeof(elements);
                  std::vector<int> vec(elements, elements + N);

                  int count = 0;
                  do
                  {
                    std::cout << ++count << ": ";
                    std::copy(vec.begin(), vec.end(),
                              std::ostream_iterator<int>(std::cout, ", "));
                    std::cout << std::endl;
                  } while (next_permutation(vec.begin(), vec.end()));
                }
                ```

                output: (4! = 24)

                ```
                1: 1, 2, 3, 4,
                2: 1, 2, 4, 3,
                3: 1, 3, 2, 4,
                4: 1, 3, 4, 2,
                5: 1, 4, 2, 3,
                6: 1, 4, 3, 2,
                7: 2, 1, 3, 4,
                8: 2, 1, 4, 3,
                9: 2, 3, 1, 4,
                10: 2, 3, 4, 1,
                11: 2, 4, 1, 3,
                12: 2, 4, 3, 1,
                13: 3, 1, 2, 4,
                14: 3, 1, 4, 2,
                15: 3, 2, 1, 4,
                16: 3, 2, 4, 1,
                17: 3, 4, 1, 2,
                18: 3, 4, 2, 1,
                19: 4, 1, 2, 3,
                20: 4, 1, 3, 2,
                21: 4, 2, 1, 3,
                22: 4, 2, 3, 1,
                23: 4, 3, 1, 2,
                24: 4, 3, 2, 1,
                ```

            生成从 N 个元素中取出 M 个的所有组合 -<

            :   ```cpp
                #include <assert.h>
                #include <algorithm>
                #include <iostream>
                #include <iterator>
                #include <vector>

                int main()
                {
                  int values[] = { 1, 2, 3, 4, 5, 6, 7 };
                  int elements[] = { 1, 1, 1, 0, 0, 0, 0 };
                  const size_t N = sizeof(elements)/sizeof(elements);
                  assert(N == sizeof(values)/sizeof(values));
                  std::vector<int> selectors(elements, elements + N);

                  int count = 0;
                  do
                  {
                    std::cout << ++count << ": ";
                    for (size_t i = 0; i < selectors.size(); ++i)
                    {
                      if (selectors[i])
                      {
                        std::cout << values[i] << ", ";
                      }
                    }
                    std::cout << std::endl;
                  } while (prev_permutation(selectors.begin(), selectors.end()));
                }
                ```

                output: (7C3 = 7*6*5/3*2*1 = 35)

                ```
                1: 1, 2, 3,
                2: 1, 2, 4,
                3: 1, 2, 5,
                4: 1, 2, 6,
                5: 1, 2, 7,
                6: 1, 3, 4,
                7: 1, 3, 5,
                8: 1, 3, 6,
                9: 1, 3, 7,
                10: 1, 4, 5,
                11: 1, 4, 6,
                12: 1, 4, 7,
                13: 1, 5, 6,
                14: 1, 5, 7,
                15: 1, 6, 7,
                16: 2, 3, 4,
                17: 2, 3, 5,
                18: 2, 3, 6,
                19: 2, 3, 7,
                20: 2, 4, 5,
                21: 2, 4, 6,
                22: 2, 4, 7,
                23: 2, 5, 6,
                24: 2, 5, 7,
                25: 2, 6, 7,
                26: 3, 4, 5,
                27: 3, 4, 6,
                28: 3, 4, 7,
                29: 3, 5, 6,
                30: 3, 5, 7,
                31: 3, 6, 7,
                32: 4, 5, 6,
                33: 4, 5, 7,
                34: 4, 6, 7,
                35: 5, 6, 7,
                ```

            用 `{make,push,pop}_heap()` 实现多路归并 -<

            :   用一台 4G 内存的机器对磁盘上的单个 100G 文件排序。

                ```cpp
                #include <algorithm>
                #include <iostream>
                #include <iterator>
                #include <vector>

                typedef int Record;
                typedef std::vector<Record> File;

                struct Input
                {
                  Record value;
                  size_t index;
                  const File* file;

                  explicit Input(const File* f)
                    : value(-1),
                      index(0),
                      file(f)
                  { }

                  bool next()
                  {
                    if (index < file->size())
                    { value = (*file)[index];
                      ++index;
                      return true;
                    } else {
                      return false;
                    }
                  }

                  bool operator<(const Input& rhs) const
                  {
                    // make_heap to build min-heap, for merging
                    return value > rhs.value;
                  }
                };

                File mergeN(const std::vector<File>& files)
                {
                  File output;
                  std::vector<Input> inputs;

                  for (size_t i = 0; i < files.size(); ++i) {
                    Input input(&files[i]);
                    if (input.next()) {
                      inputs.push_back(input);
                    }
                  }

                  std::make_heap(inputs.begin(), inputs.end());
                  while (!inputs.empty()) {
                    std::pop_heap(inputs.begin(), inputs.end());
                    output.push_back(inputs.back().value);

                    if (inputs.back().next()) {
                      std::push_heap(inputs.begin(), inputs.end());
                    } else {
                      inputs.pop_back();
                    }
                  }

                  return output;
                }

                int main()
                {
                  const int kFiles = 32;
                  std::vector<File> files(kFiles);
                  for (int i = 0; i < kFiles; ++i) {
                    File file(rand() % 1000);
                    std::generate(file.begin(), file.end(), &rand);
                    std::sort(file.begin(), file.end());
                    files[i].swap(file);
                  }

                  File output = mergeN(files);

                  std::copy(output.begin(), output.end(),
                            std::ostream_iterator<Record>(std::cout, "\n"));
                }
                ```

                类似的题目:有 a、b 两个文件,大小各是 100G 左右,每行长度不超过
                1k,这两个文件有少量(几百个)重复的行,要求用一台 4G 内存的机器找
                出这些重复行。解这道题目有两个方向,一是 hash,把 a、b 两个文件按
                行的 hash 取模分成几百个小文件,每个小文件都在 1G 以内,然后对 a1、
                b1 求交集 c1,对 a2、b2 求交集c2,这样就能在内存里解决了。

                第二个思路是外部排序,但是跟前面完整的外部排序不同,我们并不需要
                得到a’、b’ 两个已排序的文件再求交集,只需要把 a 分块排序成 100
                个小文件,再把 b 分块排序成 100 个小文件,剩下的工作就是一边读这
                些小文件,一边在内存中同时归并出 a’ 和 b’,一边求出交集。内存中的
                两个多路归并需要两个 heap,分别对应 a 和 b的小文件 s。

            用 unique() 去除连续重复空白 -<

            :   Prefer algorithm calls to hand-written loops.

                ```cpp
                struct AreBothSpaces
                {
                  bool operator()(char x, char y) const
                  {
                    return x == ' ' && y == ' ';
                  }
                };

                void removeContinuousSpaces(std::string& str)
                {
                  std::string::iterator last
                    = std::unique(str.begin(), str.end(), AreBothSpaces());
                  str.erase(last, str.end());
                }
                ```

                其实这个在上面的 erase 里面已经有了，而且代码更好。

            用 partition() 实现“调整数组顺序使得奇数位于偶数前面” -<

            :   ```cpp
                #include <algorithm>
                #include <iostream>
                #include <iterator>

                bool isOdd(int x)
                {
                  return x % 2 != 0;  // x % 2 == 1 is WRONG
                }

                void moveOddsBeforeEvens()
                {
                  int oddeven[] = { 1, 2, 3, 4, 5, 6 };
                  std::partition(oddeven, oddeven+6, &isOdd);
                  std::copy(oddeven, oddeven+6, std::ostream_iterator<int>(std::cout, ", "));
                  std::cout << std::endl;
                }

                int main()
                {
                  moveOddsBeforeEvens();

                  int oddeven[] = { 1, 2, 3, 4, 5, 6 };
                  std::stable_partition(oddeven, oddeven+6, &isOdd);
                  std::copy(oddeven, oddeven+6, std::ostream_iterator<int>(std::cout, ", "));
                  std::cout << std::endl;
                }
                ```

                复杂度是 O(N ) 时间 O(1) 空间。

                如果要顺序保持不变，可以用 `stable_partition()`，复杂度是 O(N ) 时间和 O(N ) 空间。

            用 lower_bound() 查找 IP 地址所属的城市 -<

            :   null.

            小结 -<

            :   另外,面试题的目的可能就是让你动手实现一些 STL 算法,例如求两个有序集
                合的交集 (set_intersection)、洗牌 (random_shuffle) 等等

                我个人把 STL algorithm 分为三类,面试时要求手写的往往是第二类算法。

                -   容易,即闭着眼睛一想就知道是如何实现的,自己手写一遍的难度跟
                    strlen() 和 strcpy() 差不多。这类算法基本上就是遍历一遍输入
                    区间,对每个元素做些判断或操作,一个 for 循环就解决战斗。一半
                    左右的 STL algorithm 属于此类,例如 for_each()、transform()
                    、accumulate() 等等。
                -   较 难, 知 道 思 路, 但 是 要 写 出 正 确 的 实 现 要 考 虑
                    清 楚 各 种 边 界 条 件。例如 merge()、unique()、remove()、
                    random_shuffle()、partition()、lower_bound() 等等,三成左右
                    的 STL algorithm 属于此类。
                -   难,要在一个小时内写出正确的健壮的实现基本不现实,例如 sort()、
                    nth_element()、next_permutation()、inplace_merge() 等
                    等,约有两成 STL algorithm 属于此类。

        15）C++ 编译链接模型精要 -<

        :   C++ 语言的三大约束是:与 C 兼容、零开销 (zero overhead) 原则、值语义。

            与 C 兼容书说的不是语法，而是和编译系统库的 C 语言编译器保持一致。

            查看宏展开：`gcc -E code.cpp` -<

            :   ```bash
                $ cat bind.cpp
                #define BIND(a, b) a##b
                BIND(foo, bar)

                $ gcc -E tmp.cpp
                # 1 "bind.cpp"
                # 1 "<built-in>"
                # 1 "<command-line>"
                # 1 "/usr/include/stdc-predef.h" 1 3 4
                # 1 "<command-line>" 2
                # 1 "bind.cpp"

                foobar
                ```

            值得一提的是,为了兼容 C 语言,C++ 付出了很大的代价。例如要
            **兼容 C语言的隐式类型转换规则(例如整数类型提升)**,在让 C++ 的函数重载决议
            (overload resolution) 规则无比复杂。另外 class 定义式后面那个分
            号也不晓得谋杀了多少初学者的时间。Bjarne Stroustrup自己也说“我
            又不是不懂如何设计出比 C++ 更漂亮的语言。”(由于C 语言没函数重载,
            也就不存在重载决议,所以隐式类型转换的危害没有体现在这一方面。)

            C++ 也继承了单遍编译。在单遍编译时,编译器只能根据目前看到的代码做出
            决策,读到后面的代码也不会影响前面做出的决定。这特别影响了名字查找
            (name lookup) 和函数重载决议。C++ 编译器必须在内存中保存函数级的语
            法树,才能正确实施返回值优化(RVO) 134 ,否则遇到 return 语句的时候编
            译器无法判断被返回的这个对象是不是那个可以被优化的 named object 135
            。其实由于 C++ 新增了不少语言特性,C++ 编译器并不能真正做到像 C 那样
            过眼即忘的单遍编译。但是 C++ 必须兼容 C 的语意,因此编译器不得不装得
            好像是单遍编译(准确说是单遍 parse)一样,哪怕它内部是 multiple pass
            的 136 。

            这是 C++ 的一种典型缺陷,即一样东西区分声明和定义,代码放到不同的文件
            中,这就有出现不一致的可能性。

            对于 class Foo,以下几种使用不需要看见其完整定义:

            -   定义或声明 Foo* 和 Foo&,包括用于函数参数、返回类型、局部变量、
                类成员变量等等。这是因为 C++ 的内存模型是 flat 的,Foo 的定义无
                法改变 Foo 的指针或引用的含义。
            -   声明一个以 Foo 为参数或返回类型的函数,如 Foo bar() 或 void
                bar(Foo f),但是,如果代码里调用这个函数就需要知道 Foo 的定义,因
                为编译器要使用 Foo的拷贝构造函数和析构函数,因此至少要看到它们的
                声明(虽然构造函数没有参数,但是有可能位于 private 区)。

            muduo 代码中大量使用前向声明来减少 include,并且避免把内部 class 的
            定义暴露给用户代码。

            如何判断一个 C++ 可执行文件是 debug build 还是 release build?换言之
            ,如何判断一个可执行文件是 -O0 编译还是 -O2 编译?我通常的做法是看
            class template 的短成员函数有没有被 inline 展开：

            ```bash
            $ cat vec.cc
            #include <vector>
            #include <stdio.h>
            int main()
            {
                std::vector<int> vi;
                printf(”%zd\n”, vi.size());
            }

            $ g++ -Wall vec.cc
            # non-optimized build

            $ nm ./a.out |grep size|c++filt
            00000000004007ac W std::vector<int, std::allocator<int> >::size() const
            // vector<int>::size() 没有 inline 展开,目标文件中出现了函数 (弱) 定义。

            $ g++ -Wall -O2 vec.cc # optimized build
            $ nm ./a.out |grep size|c++filt
            // 没有输出,因为 vector<int>::size() 被 inline 展开了
            ```

            在现在的 C++ 实现中,虚函数的动态调用(动态绑定、运行期决议)是透过虚
            函数表 (vtable) 进行的,每个多态 class 都应该有一份 vtable。定义或继
            承了虚函数的对象中会有一个隐含成员:指向 vtable 的指针,即 vptr。在构
            造和析构对象的时候,编译器生成的代码会修改这个 vptr 成员,这就要用到
            vtable 的定义(使用其地址)。

            总结:由于 C++ 的头文件与源文件分离,并且目标文件里没有足够的元数据供
            编译器使用,因此必须同时提供库文件和头文件。也就是说要想使用一个已经
            编译好的 C/C++ 库(无论是静态库还是动态库) ,我们需要两样东西,一是头
            文件 (.h),二是库文件 (.a 或.so),这就存在了两样东西不匹配的可能。这
            是造就 C++ 简陋脆弱的模块机制的根本原因。C++ 库之间的依赖管理远比其
            他现代语言复杂,在编写程序库和应用程序时,要熟悉各种机制的优缺点,采用
            开发及维护成本较低的方式来组织和发布库。

        16）Zero overhead 原则 -<

        :   -   <http://www.open-std.org/jtc1/sc22/wg21/docs/18015.html>
            -   <http://www.aristeia.com/c++-in-embedded.html>

        Points -<

        :   -   为什么 C++ 要引入 static_cast 之类的转型操作符,原因之一就是像
                (int*) pBuffer 这样的表达式基本上没办法用 grep 判断出它是个强制
                类型转换,写不出一个刚好只匹配类型转换的正则表达式。(again,语法
                是上下文无关的,无法用正则搞定。)
            -   那么为什么 C 语言从诞生到现在一直没有纠正这个小小的缺陷?比方说
                把O_- RDONLY, O_WRONLY, O_RDWR 分别定义为 1, 2, 3,这样 O_RDONLY
                | O_WRONLY == O_RDWR,符合直觉。而且这三个值都是宏定义,也不需要
                修改现有的源代码,只需要改改系统的头文件就行了。因 为 这 么 做
                会 破 坏 二进 制 兼 容 性。
            -   C++ ABI 的主要内容:
                -   函数参数传递的方式,比如 x86-64 用寄存器来传函数的前 4 个整数参数
                -   虚函数的调用方式,通常是 vptr/vtbl 然后用 vtbl[offset] 来调用
                -   struct 和 class 的内存布局,通过偏移量来访问数据成员
                -   name mangling
                -   RTTI 和异常处理的实现(以下本文不考虑异常处理)

-   [cpp-cheat/cpp at master · cirosantilli/cpp-cheat](https://github.com/cirosantilli/cpp-cheat/tree/master/cpp){.heart} -<

    :   -   Standards -<

            :   -   C++89
                -   C++03
                -   TR1 (Technical report 1), 2005, --> C++11
                -   TR2, C++1Y
                -   C++11 (previously known as C++0x)
                    -   lots of new features: standard passes from 800 to 1300 lines.
                    -   gcc: `-std=c++0x` -> `-std=c++11`
                -   C++14
                    -   Will come after C++11. Known as C++1Y as many have doubts it will come out in 2014.

        -   Compile time magic -<

            :   -   constexpr (C++11 only) -<

                    :   ```cpp
                        /*
                            ERROR: the compiler ensures that the function return is constexpr,
                            so this does not compile.
                        */
                        int constexpr constexpr_func_bad(){
                            return std::time();
                        }
                        #include <stdio.h>

                        constexpr long long ConstexprFactorial( long long n ) {
                            return (n == 1LL) ? 1LL : n * ConstexprFactorial(n - 1);
                            /*
                             * error: body of constexpr function
                             *      ‘constexpr int ConstexprFactorial(int)’
                             * not a return-statement
                             *
                            **/

                            //          if( n == 1LL ) {
                            //              return 1LL;
                            //          } else {
                            //              return n * ConstexprFactorial(n - 1);
                            //          }
                        }

                        int main()
                        {
                            printf( "factorial(23): %lld.\n", ConstexprFactorial(23) );

                            int n;
                            while( 1 == scanf( "%d", &n ) ) {
                                printf( "factorial(%d): %lld.\n", n, ConstexprFactorial(n) );
                            }
                        }
                        ```

                        为什么不能写成很多行（有多个 statements（`;`分隔））？这里有解释：
                        [c++ - Why is it ill-formed to have multi-line constexpr functions? - Stack Overflow](http://stackoverflow.com/questions/3226211/why-is-it-ill-formed-to-have-multi-line-constexpr-functions)。

                        ```cpp
                        // okay
                        constexpr int i = 0;

                        // okay
                        constexpr int i = 1 + 1;
                        constexpr int i2 = i;

                        // okay
                        const int i = 0;
                        constexpr int i2 = i;

                        // error: for non built-in operators, only constexpr functions can be used.
                        constexpr int i = not_constexpr_func();

                        // okay
                        constexpr int i = constexpr_func(1);

                        // error
                        constexpr int i = constexpr_func(std::time(NULL));

                        // error: cannot have constexpr to complex types, TODO rationale
                        constexpr std::string s = "abc";
                        ```

                -   static_assert (C++11 only) -<

                    :   ```cpp
                        static_assert(0 < 1, "msg");

                        // ERROR: static assertion failed
                        //static_assert(0 > 1, "msg");

                        std::srand(time(NULL));
                        // ERROR: needs to be a constexpr
                        //static_assert(std::rand() >= 0);

                        static_assert(sizeof(unsigned int) * CHAR_BIT == 32);
                        static_assert(-5 / 2 == -2);
                        ```

                -   typeid -<

                    :   ```cpp
                        #include <iostream>
                        #include <typeinfo>

                        int main()
                        {
                            int i = 0;
                            int &ri = i;
                            std::cout << "typeid(int).name() = \"" << typeid(int).name() << "\"" << std::endl;
                            std::cout << "typeid( i ).name() = \"" << typeid( i ).name() << "\"" << std::endl;
                            std::cout << "typeid( ri).name() = \"" << typeid( ri).name() << "\"" << std::endl;
                        }
                        ```

                        output:

                        ```
                        typeid(int).name() = "i"
                        typeid( i ).name() = "i"
                        typeid( ri).name() = "i"
                        ```

                        ```cpp
                        // 不能复制
                        std::type_info t = typeid(int);         // 会出错
                        std::type_index t = typeid(int);        // 这个可以

                        // 可以用 == 和 != 来比较
                        int i, i1;
                        int& ia = i;
                        class Class {};
                        Class c;
                        assert(typeid(i)  == typeid(int) );
                        assert(typeid(ia) == typeid(int&));
                        assert(typeid(i)  == typeid(i1)  );
                        assert(typeid(i)  != typeid(c)   );

                        // 自己提供 < 比较
                        struct compare {
                            bool operator ()(const type_info* a, const type_info* b) const {
                                return a->before(*b);
                            }
                        };

                        std::map<const type_info*, std::string, compare> m;

                        void f() {
                            m[&typeid(int)] = "Hello world";
                        }
                        ```

                -   auto, decltype -<

                    :   ```cpp
                        auto i = 4;
                        auto &ri = i;
                        for( auto &i : vec ) { ... }
                        for( const auto &i : vec ) { ... }

                        int i = 1;
                        std::vector<decltype(i)> v; // std::vector<int> v

                        float f = 2.0;
                        decltype(i + f) f2 = 1.5;   // float f2
                        assert(f2 == 1.5);

                        cout << 1 + 1.5 << "\n"; // "2.5"

                        // Implies reference while auto does not.
                        {
                            int i = 0;
                            int& ir = i;
                            decltype(ir) ir2 = ir;
                            ir2 = 1;
                            assert(i == 1);
                        }
                        ```

        -   nullptr -<

            :   ```cpp
                #include <cstddef>
                // basically the same
                std::nullptr_t p;
                p = NULL;
                p = 0;

                // Unlike in NULL, the size of nullptr_t is fixed.
                {
                    assert(sizeof(std::nullptr_t) == sizeof(void*));
                }
                // sizeof(NULL) 等于 sizeof(0)，估计和 int 的长度一样。
                ```

        -   refs -<

            :   ```cpp
                const int& getIntConstRef(int& i) {
                    i++;
                    return i;
                }

                class X {

                public:
                    /// Value cannot be changed.
                    const int& getPrivateConstRef() const {return this->iPrivate;}

                    // ERROR: const method cannot return noncosnt pointer!
                    //int* getPrivateAddress() const {return &this->iPrivate;}

                    // this works
                    const int* getPrivateAddressConst() const {return &this->iPrivate;}

                private:
                    int iPrivate;
                };

                void byref(int& i) {i++;}
                void bypointer(int *i) {(*i)++;}
                ```

                ```cpp
                #include <iostream>
                #include <vector>


                void assign( const int * &ip, const int *const &jp) {
                    ip = jp+1;
                }

                int main()
                {
                    int buf = { 0, 1, 2, 3 };

                    // int *p1 = &buf, *p2 = &buf;    // 为什么这个不可以？不加 const 居然报错？！
                    const int *p1 = &buf, *p2 = &buf;

                    printf( "before assign: %d %d\n", *p1, *p2 );
                    assign( p1, p2 );
                    printf( "after  assign: %d %d\n", *p1, *p2 );
                }
                /*
                before assign: 1 2
                after  assign: 3 2
                */
                ```

                refs and see also

                -   [cpp-cheat/reference.cpp at master · cirosantilli/cpp-cheat](https://github.com/cirosantilli/cpp-cheat/blob/master/cpp/reference.cpp)

        -   rvalue ref -<

            :   [cpp-cheat/rvalue_reference.cpp at master · cirosantilli/cpp-cheat](https://github.com/cirosantilli/cpp-cheat/blob/master/cpp/rvalue_reference.cpp)

        -   functions -<

            :   -   函数定义、重载 -<

                    :   ```cpp
                        // 首先，返回值不能用来区别函数，这点没有再必要讨论

                        // ERROR: no compound literals in C++
                        void foo (int bar[] = (int){0 ,1});

                        // 实际上，下面三个一样一样的
                        void funtion( int buf[] );
                        void funtion( int buf );
                        void funtion( int *buf );

                        // Okay
                        std::string overload(float i) { return "f"; }
                        std::string overload(int i, int j) { return "ii"; }
                        std::string overload(float i, float j, float k = 0.f) { return "iff"; }

                        // 这三个冲突了
                        void overload(int i) {}
                        void overload(const int i){}
                        void overload(int i, int j=0){cout << "int int=";}

                        // 为什么他们不冲突???
                        void overloadValAddr(const int i) {}
                        void overloadValAddr(const int& i) {}

                        // 可以
                        void defaultArgCase1(int i=0);
                        void defaultArgCase1(int i  ) { printf("case1: default is %d.\n", i); }

                        // 可以
                        void defaultArgCase2(int i);
                        void defaultArgCase2(int i=1) { printf("case2: default is %d.\n", i); }

                        // 不可以
                        void defaultArgCase3(int i=3);
                        void defaultArgCase3(int i=4) { printf("case3: default is %d.\n", i); }
                        ```

                        下面这个也是可以的：

                        ```cpp
                        #include <iostream>
                        #include <stdio.h>

                        class X {
                        public:
                            void func( int i );
                        };

                        void X::func( int i = 123 ) { printf("default is: %d.\n", i); }

                        int main()
                        {
                            X x;
                            x.func();
                        }
                        ```

                        但如果你把 X 声明再 X.h 实现再 X.cpp，然后 include 进来 X.h，你就不能用 `x.func()` 了，
                        因为 include 进来的头文件没有 default arg。务必留意这一点。因为我们实践中通常都是这种情况。

                        总结，defalut arguments 在声明和实现的时候“告诉”编译器都可以，但是不能“告诉”两次。
                        注意从头文件引入的时候，你能看到什么，看不到什么，reason 一下其实这些规则都是合理的。

                        ```cpp
                        std::string (*fi)(int) = overload;
                        std::string (*ff)(float) = overload;

                        // Uses the void f(char c); overload
                        std::for_each(s.begin(), s.end(), static_cast<void (*)(char)>(&f));
                        // Uses the void f(int i); overload
                        std::for_each(s.begin(), s.end(), static_cast<void (*)(int)>(&f));

                        // The compiler will figure out which f to use according to
                        // the function pointer declaration.
                        void (*fpc)(char) = &f;
                        std::for_each(s.begin(), s.end(), fpc); // Uses the void f(char c); overload
                        void (*fpi)(int) = &f;
                        std::for_each(s.begin(), s.end(), fpi); // Uses the void f(int i); overload

                        transform (numbers.begin(), numbers.end(), lengths.begin(), mem_fun(&string::length));
                        ```

                        refs and see also

                        -   [mem_fun - C++ Reference](http://www.cplusplus.com/reference/functional/mem_fun/)

                -   operator overload -<

                    :   如此详细，我直接 copy 过来了：

                        ```cpp
                        @include <-=include/operator_overload.cpp=
                        ```

                        refs and see also

                        -   [cpp-cheat/operator_overload.cpp at master · cirosantilli/cpp-cheat](https://github.com/cirosantilli/cpp-cheat/blob/master/cpp/operator_overload.cpp)

-   [Generic programming - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Generic_programming)

-   [编程小谈 — RAII与Pimpl | UC技术博客](http://tech.uc.cn/?p=851)

-   [Opaque pointer - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/Opaque_pointer)

    :    `[o'pek]` 不透明。

        Opaque pointers are a way to hide the implementation details of an
        interface from ordinary clients, so that the implementation may be
        changed without the need to recompile the modules using it. This
        benefits the programmer as well since a simple interface can be
        created, and most details can be hidden in another file. This is
        important for providing binary code compatibility through different
        versions of a shared library, for example.

        This technique is described in Design Patterns as the Bridge pattern.
        It is sometimes referred to as "handle classes", the "Pimpl idiom" (for
        "pointer to implementation idiom"), "Compiler firewall idiom",
        "d-pointer" or "Cheshire Cat", especially among the C++ community.

        -   C example -<

            :   -   obj.h -<

                    :   ```cpp
                        struct obj;

                        /*
                         * The compiler considers struct obj an incomplete type. Incomplete types
                         * can be used in declarations.
                         */

                        size_t obj_size(void);

                        void obj_setid(struct obj *, int);

                        int obj_getid(struct obj *);
                        ```

                -   obj.cpp -<

                    :   ```cpp
                        #include "obj.h"

                        struct obj {
                            int id;
                        };

                        /*
                         * The caller will handle allocation.
                         * Provide the required information only
                         */

                        size_t obj_size(void) {
                            return sizeof(struct obj);
                        }

                        void obj_setid(struct obj *o, int i) {
                            o->id = i;
                        }

                        int obj_getid(struct obj *o) {
                            return o->id;
                        }
                        ```

                This example demonstrates a way to achieve the information hiding
                (encapsulation) aspect of object-oriented programming using the C
                language. If someone wanted to change the declaration of struct
                obj, it would be unnecessary to recompile any other modules in the
                program that use the obj.h header file unless the API was also
                changed. Note that it may be desirable for the functions to check
                that the passed pointer is not NULL, but such checks have been
                omitted above for brevity.

        -   C++ example -<

            :   -   Class.h -<

                    :   ```cpp
                        class PublicClass {
                        public:
                            PublicClass();                              // Constructor
                            PublicClass(const PublicClass&);            // Copy constructor
                            PublicClass(PublicClass&&);                 // Move constructor
                            PublicClass& operator=(const PublicClass&); // Copy assignment operator
                            ~PublicClass();                             // Destructor
                            // Other operations...

                        private:
                            struct CheshireCat;                         // Not defined here
                            unique_ptr<CheshireCat> d_ptr;              // opaque pointer
                        };
                        ```

                -   Class.cpp -<

                    :   ```cpp
                        //CPP file:
                        #include "PublicClass.h"

                        struct PublicClass::CheshireCat {
                            int a;
                            int b;
                        };

                        PublicClass::PublicClass()
                            : d_ptr(new CheshireCat()) {
                            // do nothing
                        }

                        PublicClass::PublicClass(const PublicClass& other)
                            : d_ptr(new CheshireCat(*other.d_ptr)) {
                            // do nothing
                        }

                        PublicClass::PublicClass(PublicClass&& other) = default;

                        PublicClass& PublicClass::operator=(const PublicClass &other) {
                            *d_ptr = *other.d_ptr;
                            return *this;
                        }

                        PublicClass::~PublicClass() = default;
                        ```

                One type of opaque pointer commonly used in C++ class
                declarations is the d-pointer. The d-pointer is the only
                private data member of the class and points to an instance of a
                struct. Named by Arnt Gulbrandsen of Trolltech, this method
                allows class declarations to omit private data members, except
                for the d-pointer itself.[6] The result: (a) more of the class
                implementation is hidden from view; (b) adding new data members
                to the private struct does not affect binary compatibility; (c)
                the header file containing the class declaration only needs to
                #include those other files needed for the class interface,
                rather than for its implementation. One side benefit is that
                compilations are faster because the header file changes less
                often. The d-pointer is heavily used in the Qt and KDE
                libraries.

- [Here be dragons: advances in problems you didn’t even know you had | teideal glic deisbhéalach](http://www.serpentine.com/blog/2011/06/29/here-be-dragons-advances-in-problems-you-didnt-even-know-you-had/)
- [function/bind的救赎（上） - 孟岩 - 博客频道 - CSDN.NET](http://blog.csdn.net/myan/article/details/5928531)

-   虚基类????

-   http://en.wikipedia.org/wiki/Return_value_optimization

-   如果用 C++ 实现 BigInteger

    ```cpp
    BigInteger factorial(int n)
    {
        BigInteger result(1);
        for (int i = 1; i <= n; ++i) {
            result *= i;
        }
    return result;
    }
    ```

-   snippets

    :   ```cpp
        std::srand(time(NULL));

        // 哪里有错？
        #include <iostream>
        #include <stdio.h>

        struct compare {
            bool operator()( int *a, int *b ) const {
                return *a > *b;
            }
        };

        int main()
        {
            using std::cout;
            int a, b;
            while( 2 == scanf("%d %d", &a, &b) ) {
                if( compare(&a,&b) ) {
                    cout << "big\n";
                } else {
                    cout << "small\n";
                }
            }
        }
        ```

