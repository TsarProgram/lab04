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
>cmake_minimum_required(VERSION 3.4) //проверка версии CMake
>project(formatter) //создаем проект formatter
>set(CMAKE_CXX_STANDARD 11) //установка стандарта языка
>set(CMAKE_CXX_STANDARD_REQUIRED ON)
>add_library(formatter STATIC \${CMAKE_CURRENT_SOURCE_DIR}/formatter.cpp) //компилируем библиотеку formatter
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}) //подключаем заголовочные файлы
>EOF
$ cmake -H. -B_build  //создаем директорию для сборки
$ cmake --build _build //собираем проект
$ ls _build/libformatter.a //проверка библиотеки formatter на наличие
Вывод: _build/libformatter.a //прошла успешно
```

### Задание 2
2. [x] У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.
```sh
$ cd .. //выходим из formatter_lib
cd formatter_ex_lib/ //переходи в папку formatter_ex_lib
$ cat >> CMakeLists.txt <<EOF //создаем CMakeLists.txt
>cmake_minimum_required(VERSION 3.4) //проверка версии CMake
>project(formatter_ex) //создаем проект formatter_ex
>set(CMAKE_CXX_STANDARD 11) //установка стандарта языка
>set(CMAKE_CXX_STANDARD_REQUIRED ON)
>set(CMAKE_CURRENT_SOURCE_DIR /home/vitaliy/test) //изменяем переменную CMAKE_CURRENT_SOURCE_DIR
>add_library(formatter_ex STATIC \${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib/formatter_ex.cpp) //компилируем библиотеку formatter_ex
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib) //подключаем заголовочные файлы
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib)
>target_link_libraries(formatter_ex formatter) //связываем библиотеки
>EOF
$ cmake -H. -B_build //создаем директорию для сборки
$ cmake --build _build //собираем проект
$ ls _build/libformatter_ex.a //проверка библиотеки formatter_ex на наличие
Вывод: _build/libformatter_ex.a //прошла успешно
```

### Задание 3
3. [x]  Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:

*hello_world*, которое использует библиотеку *formatter_ex*;
```sh
$ cd .. //выходим из formatter_ex_lib
$ cd hello_world_application/ //переходи в папку hello_world_application
$ cat >> CMakeLists.txt <<EOF //создаем CMakeLists.txt
>cmake_minimum_required(VERSION 3.4) //проверка версии CMake
>project(hello_world) //создаем проект hello_world
>set(CMAKE_CXX_STANDARD 11) //установка стандарта языка
>set(CMAKE_CXX_STANDARD_REQUIRED ON)
>set(CMAKE_CURRENT_SOURCE_DIR /home/vitaliy/test) //изменяем переменную CMAKE_CURRENT_SOURCE_DIR
>add_executable(hello_world \${CMAKE_CURRENT_SOURCE_DIR}/hello_world_application/hello_world.cpp) //подключаем cpp
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib) //подключаем заголовочные файлы
>include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib)
>find_library(formatter_ex NAMES libformatter_ex.a PATHS \${CMAKE_CURRENT_SOURCE_DIR}/formatter_ex_lib) //поиск библиотек formatter_ex
>find_library(formatter NAMES libformatter.a PATHS \${CMAKE_CURRENT_SOURCE_DIR}/formatter_lib) //и formatter
>target_link_libraries(hello_world \${formatter_ex} \${formatter}) //связываем библиотеки
>EOF
$ cmake -H. -B_build //создаем директорию для сборки
$ cmake --build _build //собираем проект
$ _build/hello_world && echo //проверка
Вывод:
-------------------------
hello, world!
-------------------------

```
*solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.
```sh
$ cd .. //выходим из hello_world_application
$ cd solver_lib/ //заходим в solver_lib/
$ cat >> CMakeLists.txt <<EOF  //см. задание 1
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

$ cd ..  //выходим из solver_lib/
$ cd solver_application/ //заходим в solver_application
$ cat >> CMakeLists.txt <<EOF //см. задание 2
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
```
**Удачной стажировки!**
**Спасибо))**
