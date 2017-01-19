---
title: 404
date: 2016-08-18 17:15:23
permalink: /404
---

```cpp
#include <iostream>
#define PAGEFOUND 1
#define PAGENOTFOUND 0

using namespace std;

int main(int argc, char const *argv[])
{
    int pgFound = 0;

    if(pgFound == PAGENOTFOUND)
        cout << "                                                                                   \
                            ***             *******************                    **               \
                           ****             **               **                   ***               \
                          ** **             **               **                 ** **               \
                         **  **             **               **                **  **               \
                        **   **             **               **               **   **               \
                       **    **             **               **              **    **               \
                     **      **             **               **             **     **               \
                    **       **             **               **            **      **               \
                   **        **             **               **           **       **               \
                  *******************       **               **          ******************         \
                  *******************       **               **          ******************         \
                             **             **               **                    **               \
                             **             **               **                    **               \
                             **             **               **                    **               \
                             **             **               **                    **               \
                             **             *******************                    **               \
                "
            << endl;
    else
        cout << "Hey, bro. Nice to meet you." << endl;
    return 0;
}
```