[![Build Status](https://travis-ci.com/snoreoh/lab4.svg?branch=master)](https://travis-ci.org/snoreoh/lab4)
## Лабораторная работа #4

## Выполнение:

### Задание 1
1. [x] Вы продолжаете проходить стажировку в "Formatter Inc." (см подробности).

В прошлый раз ваше задание заключалось в настройке автоматизированной системы CMake.

Сейчас вам требуется настроить систему непрерывной интеграции для библиотек и приложений, с которыми вы работали в прошлый раз. Настройте сборочные процедуры на различных платформах:

используйте TravisCI для сборки на операционной системе Linux с использованием компиляторов gcc и clang;

ТЕКСТ .travis.yml:
```sh
language: cpp
script:
- cd solver_application
- cmake .
- make
- cd ..
- cd hello_world_application
- cmake .
- make
addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake
      - cmake-data
```
P.S. после переноса всех файлов библиотек в одну папку должно заработать)

