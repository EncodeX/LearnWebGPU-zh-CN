Настройка проекта <span class="bullet">🟢</span>
=============

```{translation-warning} Outdated Translation, /introduction.md
This is a **community translation** of [the original English page](%original%), which **has been updated** since it was translated and may thus no longer be in sync. You are welcome to [contribute](%contribute%)!
```

```{admonition} Incomplete Translation
This is a **community translation** of [the original English page](/introduction.md), which is **not fully translated yet**. You are welcome to [contribute](https://github.com/eliemichel/LearnWebGPU/edit/main/translation/fr/introduction.md)!
```

```{lit-setup}
:tangle-root: 000 - Project setup
```

*Итоговый код:* [`step000`](https://github.com/eliemichel/LearnWebGPU-Code/tree/step000)

В нашем примере мы используем [CMake](https://cmake.org/) для организации компиляции кода. Это очень стандартный способ управления кросс-платформенными сборками, и мы следуем идиомам [современного CMake](https://cliutils.gitlab.io/modern-cmake/).

Требования
------------

Все, что нам нужно, это CMake и компилятор C++. Инструкции подробно описаны ниже для каждой ОС.

```{hint}
После установки вы можете ввести `which cmake` (Linux/macOS) или `where cmake` (Windows), чтобы проверить, находит ли ваша командная строка полный путь к команде `cmake`. Если нет, убедитесь, что ваша переменная окружения `PATH` содержит директорию, в которой установлен CMake.
```

### Linux

Если вы используете дистрибутив Ubuntu/Debian, установите следующие пакеты:

```bash
sudo apt install cmake build-essential
```

Другие дистрибутивы имеют эквивалентные пакеты, убедитесь, что у вас работают команды `cmake`, `make` и `g++`.

### Windows

Скачайте и установите CMake с [официальной страницы загрузки](https://cmake.org/download/). Вы можете использовать либо [Visual Studio](https://visualstudio.microsoft.com/downloads/), либо [MinGW](https://www.mingw-w64.org/) в качестве набора инструментов для компиляции.

### MacOS

Вы можете установить CMake с помощью `brew install cmake`, а [XCode](https://developer.apple.com/xcode/) для сборки проекта.

Минимальный проект
---------------

Самый минимальный проект состоит из файла с исходным кодом `main.cpp` и файла сборки `CMakeLists.txt`.

Начнем с классического "Hello, world!" в `main.cpp`:

```{lit} C++, файл: main.cpp
#include <iostream>

int main (int, char**) {
	std::cout << "Hello, world!" << std::endl;
	return 0;
}
```

В `CMakeLists.txt` мы указываем, что хотим создать *target* типа *executable* с именем "App" (это также будет имя исполняемого файла), и чей исходный код находится в `main.cpp`:

```{lit} CMake, Устанавливаем app target
add_executable(App main.cpp)
```

CMake  также ожидает в начале файла `CMakeLists.txt` указания версии CMake, для которой написан файл (минимальная поддерживаемая...ваша версия), и некоторой информации о проекте:

```{lit} CMake, файл: CMakeLists.txt
cmake_minimum_required(VERSION 3.0...3.25)
project(
	LearnWebGPU # имя проекта, которое также будет именем решения Visual Studio, если вы его используете
	VERSION 0.1.0 # любая версия
	LANGUAGES CXX C # языки программирования, используемые в проекте
)

{{Устанавливаем app target}}

{{Рекомендуемые дополнения}}
```

Сборка
--------

Теперь мы готовы собрать наш минимальный проект. Откройте терминал и перейдите в директорию, где находятся файлы `CMakeLists.txt` и `main.cpp`:

```bash
cd your/project/directory
```

```{hint}
В окне проводника Windows, показывающем директорию вашего проекта, нажмите Ctrl+L, затем введите `cmd` и нажмите Enter. Это откроет терминал в текущей директории.
```

Теперь попросим CMake создать файлы сборки для нашего проекта. Мы просим его изолировать файлы сборки от нашего исходного кода, поместив их в директорию *build/* с помощью опции `-B build`. Это очень рекомендуется, чтобы легко отличать сгенерированные файлы от тех, которые мы написали вручную (т.е. исходные файлы):

```bash
cmake . -B build
```

Это создает файлы сборки для `make`, Visual Studio или XCode  в зависимости от вашей системы (можно использовать `-G` чтобы указать конкретную систему сборки, см. `cmake -h` для получения дополнительной информации). Чтобы окончательно собрать программу и сгенерировать исполняемый файл `App` (или `App.exe`), вы можете либо открыть сгенерированное решение Visual Studio или XCode, либо ввести в терминале:

```bash
cmake --build build
```

Затем запустите полученную программу:

```bash
build/App  # linux/macOS
build\Debug\App.exe  # Windows
```

Рекомендуемые дополнения
------------------

Мы настроим некоторые свойства таргета App, вызвав команду `set_target_properties` где-то после `add_executable`.

```{lit} CMake, Рекомендуемые дополнения
set_target_properties(App PROPERTIES
	CXX_STANDARD 17
	CXX_STANDARD_REQUIRED ON
	CXX_EXTENSIONS OFF
	COMPILE_WARNING_AS_ERROR ON
)
```

Свойство `CXX_STANDARD` установлено на 17, чтобы указать, что мы требуем C++17 (это позволит нам использовать некоторые синтаксические трюки позже, но не является обязательным). Свойство `CXX_STANDARD_REQUIRED` гарантирует, что конфигурация завершится ошибкой, если C++17 не поддерживается.

Свойство `CXX_EXTENSIONS` установлено на `OFF`, чтобы отключить специфичные для компилятора расширения (например, в GCC это заставит CMake использовать `-std=c++17` вместо `-std=gnu++17` в списке флагов компиляции).

Свойство `COMPILE_WARNING_AS_ERROR` включено как хорошая практика, чтобы убедиться, что никакие предупреждения не остаются без внимания. Предупреждения действительно важны, особенно при изучении нового языка/библиотеки. Чтобы убедиться, что у нас есть как можно больше предупреждений, мы добавляем некоторые опции компиляции:

```{lit} CMake
if (MSVC)
	target_compile_options(App PRIVATE /W4)
else()
	target_compile_options(App PRIVATE -Wall -Wextra -pedantic)
endif()
```

```{note}
В сопровождающем коде я скрыл эти детали в функции `target_treat_all_warnings_as_errors()` определенной в `utils.cmake` и включенной в начале файла `CMakeLists.txt`.
```

На MacOS CMake может генерировать файлы проекта XCode. Однако по умолчанию не создаются *схемы*, и XCode сам генерирует схему для каждой цели CMake -- обычно нам нужна только схема для нашей основной цели. Поэтому мы устанавливаем свойство `XCODE_GENERATE_SCHEME`.
Мы также уже включим захват кадров для отладки GPU

```{lit} CMake, Рeкомендуемые дополнения (добавляем для XCode)
if (XCODE)
	set_target_properties(App PROPERTIES
		XCODE_GENERATE_SCHEME ON
		XCODE_SCHEME_ENABLE_GPU_FRAME_CAPTURE_MODE "Metal"
	)
endif()
```

Заключение
----------

Теперь у нас есть хорошая **базовая конфигурация проекта**, на которую мы будем опираться в следующих главах. В следующих главах мы увидим, как [интегрировать WebGPU](hello-webgpu.md) в наш проект, как [инициализировать его](adapter-and-device/index.md), и как [открыть окно](opening-a-window.md) для отрисовки.

*Итоговый код:* [`step000`](https://github.com/eliemichel/LearnWebGPU-Code/tree/step000)
