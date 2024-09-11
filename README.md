<div align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/eliemichel/LearnWebGPU/main/images/webgpu-dark.svg">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/eliemichel/LearnWebGPU/main/images/webgpu-light.svg">
    <img alt="Learn WebGPU Logo" src="images/webgpu-dark.svg" width="200">
  </picture>

  <a href="https://github.com/eliemichel/LearnWebGPU">LearnWebGPU</a> &nbsp;|&nbsp; <a href="https://github.com/eliemichel/WebGPU-Cpp">WebGPU-C++</a> &nbsp;|&nbsp; <a href="https://github.com/eliemichel/WebGPU-distribution">WebGPU-distribution</a><br/>
  <a href="https://github.com/eliemichel/glfw3webgpu">glfw3webgpu</a> &nbsp;|&nbsp; <a href="https://github.com/eliemichel/sdl2webgpu">sdl2webgpu</a>
  
  <a href="https://discord.gg/2Tar4Kt564"><img src="https://img.shields.io/static/v1?label=Discord&message=Join%20us!&color=blue&logo=discord&logoColor=white" alt="Discord | Join us!"/></a>
</div>

Learn WebGPU
============

This is the source files of the website [https://eliemichel.github.io/LearnWebGPU](https://eliemichel.github.io/LearnWebGPU).

Building
--------

Building the website requires Python.

1. It is recommended, but not mandatory, to set up a virtual Python environment:

```bash
# Create the virtualenv in the "venv" directory (only the first time)
virtualenv venv
# Activate the virtualenv (each time you open a new terminal)
venv/Scripts/activate
```

2. Then install Python packages

```bash
pip install -r requirements.txt
```

3. And finally build the website by running:

```bash
make html
```

4. To open the new website, run the following simple server:

```bash
cd _build/html
python -m http.server 8000
```

Then browse to [http://localhost:8000](http://localhost:8000). You should see the LearnWebGPU website there.

5. To build the source code defined by [Sphinx Literate](https://github.com/eliemichel/sphinx_literate) (optional), run

```bash
make tangle
```

Contributing
------------

To help this project, you can:

 - Consult the [Contributing](https://eliemichel.github.io/LearnWebGPU/contributing) section of the guide.
 - Report [Issues](https://github.com/eliemichel/LearnWebGPU/issues) and suggest [Pull Requests](https://github.com/eliemichel/LearnWebGPU/pulls).
 - [Join Discord](https://discord.gg/2Tar4Kt564) for more information.

