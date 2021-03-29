## Лабораторная работа #3

## Выполнение:

### Задание 1
1. [x] Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.
```sh
$ git clone https://github.com/tp-labs/lab03 test/ //копируем исходники в локальный репозиторий
$ cd test/formatter_lib //переходи в папку formatter_lib
$ cat >> CMakeLists.txt <<EOF //создаем CMakeLists.txt
>cmake_minimum_required(VERSION 3.4)
>project(formatter)
>set(CMAKE_CXX_STANDARD 11)
>set(CMAKE_CXX_STANDARD_REQUIRED ON)
>add_library(formatter STATIC \${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp) 
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR})
>EOF
$ cmake -H. -B_build
$ cmake --build _build
$ ls _build/libformatter.a
Вывод: _build/libformatter.a
```

### Задание 2
2. [x] У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.
```sh
$ cd ..
cd formatter_ex_lib/
$ cat >> CMakeLists.txt <<EOF
>cmake_minimum_required(VERSION 3.4)
>project(formatter_ex)
>set(CMAKE_CXX_STANDARD 11)
>set(CMAKE_CXX_STANDARD_REQUIRED ON)
>set(CMAKE_CURRENT_SOURCE_DIR /home/vitaliy/test)
>add_library(formatter_ex STATIC \${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib/formatter_ex.cpp) 
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib)
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib)
>target_link_libraries(formatter_ex formatter)
>EOF
$ cmake -H. -B_build
$ cmake --build _build
Вывод: _build/libformatter_ex.a
```

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
3. [x]  *hello_world*, которое использует библиотеку *formatter_ex*;
```sh
$ cd ..
$ cd hello_world_application/
$ cat >> CMakeLists.txt <<EOF
>cmake_minimum_required(VERSION 3.4)
>project(hello_world)
>set(CMAKE_CXX_STANDARD 11)
>set(CMAKE_CXX_STANDARD_REQUIRED ON)
>set(CMAKE_CURRENT_SOURCE_DIR /home/vitaliy/test)
>add_executable(hello_world \${CMAKE_CURRENT_SOURCE_DIR}/hello_world_application/hello_world.cpp) 
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib)
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib)
>find_library(formatter_ex NAMES libformatter_ex.a PATHS \${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib)
>find_library(formatter NAMES libformatter.a PATHS \${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib)
>target_link_libraries(hello_world \${formatter_ex} \${formatter})
>EOF
$ cmake -H. -B_build
$ cmake --build _build
$ _build/hello_world && echo
Вывод:
-------------------------
hello, world!
-------------------------

```
4. [x]  *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.
```sh
$ cd ..
$ cd solver_lib/
$ cat >> CMakeLists.txt <<EOF
>cmake_minimum_required(VERSION 3.4)
>project(solver_lib)
>set(CMAKE_CXX_STANDARD 11)
>set(CMAKE_CXX_STANDARD_REQUIRED ON)
>add_library(solver_lib STATIC \${CMAKE_CURRENT_SOURCE_DIR}/solver.cpp) 
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR})
>EOF
$ cmake -H. -B_build
$ cmake --build _build
ERROR!!!: 'sqrtf' is not a member of std
$ nano solver.cpp // 1) добавляем строку #include <cmath>
                  // 2) изменяем 'sqrtf' на 'sqrt'
$ cmake --build _build
$ ls _build/libsolver_lib.a
Вывод: _build/libsolver_lib.a

$ cd ..
$ cd solver_application/
$ cat >> CMakeLists.txt <<EOF
>cmake_minimum_required(VERSION 3.4)
>project(solver)
>set(CMAKE_CXX_STANDARD 11)
>set(CMAKE_CXX_STANDARD_REQUIRED ON)
>set(CMAKE_CURRENT_SOURCE_DIR /home/vitaliy/test)
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/solver_lib) 
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib)
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib)
>add_executable(solver \${CMAKE_CURRENT_SOURCE_DIR}/solver_application/equation.cpp)
>find_library(formatter_ex NAMES libformatter_ex.a PATHS \${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib/_build)
>find_library(formatter NAMES libformatter.a PATHS \${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib/_build)
>find_library(solver_lib NAMES libsolver_lib.a PATHS \${CMAKE_CURRENT_SOURCE_DIR}/solver_lib/_build)
>target_link_libraries(solver \${formatter_ex} \${formatter} \${solver_lib})
>EOF
$ cmake -H. -B_build
$ cmake --build _build
$ _build/hello_world && echo
Вывод:
4
1
3
-------------------------
error: discriminant < 0
------------------------- 
**Удачной стажировки!**
**Спасибо))**
