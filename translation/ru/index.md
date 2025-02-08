Изучаем WebGPU
============

*Для нативной графики в C++.*

Эта документация проведёт вас через использование графического API [WebGPU](https://www.w3.org/TR/webgpu) для создания **нативных 3D-приложений** на C++ с нуля, для Windows, Linux и macOS.

`````{admonition} Быстрый старт! (Кликни)
:class: foldable quickstart

*Хотите понимать каждый бит GPU-кода, который пишете?*

````{admonition} Да, писать WebGPU-код **с нуля**!
:class: foldable yes

Отлично! Просто переходите к [введению](introduction.md) и **читайте все главы** последовательно.
````

````{admonition} Нет, я бы хотел **пропустить начальный этап**.
:class: foldable no

Это вполне логично, вы всегда можете **вернуться к [базовым шагам](getting-started/index.md) позже**.

Возможно, вам стоит сразу посмотреть ссылку на _**готовый код**_, которая находится в начале и конце **каждой страницы**, например:

```{image} /images/intro/resulting-code-light.png
:class: only-light with-shadow
```

```{image} /images/intro/resulting-code-dark.png
:class: only-dark with-shadow
```

*Вы бы хотели использовать упрощенную версию?*

```{admonition} Да я предпочту **стиль C++**.
:class: foldable yes

Используйте вкладку "**With webgpu.hpp**".
```

```{admonition} Нет, покажи мне **чистый C WebGPU API**!
:class: foldable no

Используйте вкладку "**Vanilla webgpu.h**". *Готовый код* для чистого WebGPU обновляется реже, но эта вкладка также переключает **все блоки кода** внутри руководства, они **актуальны**.
```

Чтобы **собрать этот базовый код**, обратитесь к разделу [Сборка](getting-started/project-setup.md#building) главы о настройке проекта. Вы можете добавить `-DWEBGPU_BACKEND=WGPU` (по умолчанию) или `-DWEBGPU_BACKEND=DAWN` к строке `cmake -B build`, чтобы выбрать соответственно [`wgpu-native`](https://github.com/gfx-rs/wgpu-native) или [Dawn](https://dawn.googlesource.com/dawn/) в качестве бэкенда.

*Как далеко вы хотите продвинуться с базовым кодом?*

```{admonition} Простой треугольник
:class: foldable quickstart

Обратитесь к главе [Привет, Треугольник](basic-3d-rendering/hello-triangle.md).
```

```{admonition} 3D Mesh Viewer с базовым управлением
:class: foldable quickstart

Рекомендую начать с конца главы [Контроль освещения](basic-3d-rendering/some-interaction/lighting-control.md).
```

````

```{admonition} Я хочу, чтобы код **работал и в вебе**.
:class: foldable warning

Основная часть руководства не включает несколько дополнительных строк кода. Обратитесь к приложению [Сборка для веба](appendices/building-for-the-web.md), чтобы **адаптировать примеры** для запуска в вебе!
```

`````

```{admonition}  🚧 Work in progress
Этот гайд всё ещё **находится в разработке**, а **стандарт WebGPU продолжает эволюционировать**. Чтобы помочь читателю отслеживать его актуальность, мы используем следующие значки в заголовках глав:

🟢 **Актуально!** *Использует последнюю версию [WebGPU-distribution](https://github.com/eliemichel/WebGPU-distribution).*  
🟡 **Готово к чтению**, *но использует устаревшую версию WebGPU.*  
🟠 **В процессе работы**: *читаемо, но не завершено.*  
🔴 **TODO**: *поверхностно рассмотренная тема.*  

**NB:** При использовании кода, сопровождающего главу, убедитесь, что используете **именно ту версию** `webgpu/`, которая предоставляется в ней, чтобы избежать несовместимостей.
```


Содержание
--------

```{toctree}
:titlesonly:

introduction
getting-started/index
basic-3d-rendering/index
basic-compute/index
advanced-techniques/index
appendices/index
```