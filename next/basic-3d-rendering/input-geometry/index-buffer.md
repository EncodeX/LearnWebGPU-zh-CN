Index Buffer <span class="bullet">🟢</span>
============

```{lit-setup}
:tangle-root: 034 - Index Buffer - Next - vanilla
:parent: 033 - Multiple Attributes - Option A - Next - vanilla
:alias: Vanilla
```

```{lit-setup}
:tangle-root: 034 - Index Buffer - Next
:parent: 033 - Multiple Attributes - Option A - Next
```

````{tab} With webgpu.hpp
*Resulting code:* [`step034-next`](https://github.com/eliemichel/LearnWebGPU-Code/tree/step034-next)
````

````{tab} Vanilla webgpu.h
*Resulting code:* [`step034-vanilla-next`](https://github.com/eliemichel/LearnWebGPU-Code/tree/step034-vanilla-next)
````

The index buffer is used to **separate** the list of **vertex attributes** from the actual order in which they are **connected**. To illustrate its interest, let us draw a square, which is made of 2 triangles.

```{themed-figure} /images/quad-{theme}.plain.svg
:align: center
```

Index data
----------

A straightforward way of drawing such a square is to use the following vertex attribtues:

```C++
std::vector<float> vertexData = {
	// Triangle #0
	-0.5, -0.5, // A
	+0.5, -0.5,
	+0.5, +0.5, // C

	// Triangle #1
	-0.5, -0.5, // A
	+0.5, +0.5, // C
	-0.5, +0.5,
};
```

But as you can see some **data is duplicated** (points $A$ and $C$). And this duplication could be much worst on larger shapes with connected triangles.

A more compact way of expressing the square's geometry is to separate the **position** from the **connectivity**:

```C++
// Define point data
// The de-duplicated list of point positions
std::vector<float> pointData = {
	-0.5, -0.5, // Point #0 (A)
	+0.5, -0.5, // Point #1
	+0.5, +0.5, // Point #2 (C)
	-0.5, +0.5, // Point #3
};

// Define index data
// This is a list of indices referencing positions in the pointData
std::vector<uint16_t> indexData = {
	0, 1, 2, // Triangle #0 connects points #0, #1 and #2
	0, 2, 3  // Triangle #1 connects points #0, #2 and #3
};
```

The index data must have type `uint16_t` or `uint32_t`. The former is more compact but limited to $2^{16} = 65 536$ vertices.

````{note}
I also keep the interleaved color attribute in this example, my point data is:

```{lit} C++, Define point data (also for tangle root "Vanilla")
std::vector<float> pointData = {
	// x,   y,     r,   g,   b
	-0.5, -0.5,   1.0, 0.0, 0.0,
	+0.5, -0.5,   0.0, 1.0, 0.0,
	+0.5, +0.5,   0.0, 0.0, 1.0,
	-0.5, +0.5,   1.0, 1.0, 0.0
};
```

```{lit} C++, Define index data (hidden, also for tangle root "Vanilla")
// This is a list of indices referencing positions in the pointData
std::vector<uint16_t> indexData = {
	0, 1, 2, // Triangle #0 connects points #0, #1 and #2
	0, 2, 3  // Triangle #1 connects points #0, #2 and #3
};
```

Using the index buffer adds an **overhead** of `6 * sizeof(uint16_t)` = 12 bytes **but also saves** `2 * 5 * sizeof(float)` = 40 bytes, so even on this very simple example it is worth using.
````

This split of data reorganizes our buffer initialization method:

```{lit} C++, InitializeBuffers method (replace, also for tangle root "Vanilla")
bool Application::InitializeBuffers() {
	{{Define point data}}
	{{Define index data}}
	
	// We now store the index count rather than the vertex count
	m_indexCount = static_cast<uint32_t>(indexData.size());

	{{Create point buffer}}
	{{Create index buffer}}
	return true;
}
```

````{topic} Terminology
I usually replace the name **vertex** data with **point** data when referring to the de-duplicated attribute buffer. In other terms, `vertex[i] = points[index[i]]`. The name *vertex* is used to mean a **corner** of triangle, i.e., a pair of a point and a triangle that uses it.

```{lit} C++, Create point buffer (hidden)
// Create point buffer
BufferDescriptor bufferDesc = Default;
bufferDesc.size = pointData.size() * sizeof(float);
bufferDesc.usage = BufferUsage::CopyDst | BufferUsage::Vertex; // Vertex usage here!
m_pointBuffer = m_device.createBuffer(bufferDesc);

// Upload geometry data to the buffer
m_queue.writeBuffer(m_pointBuffer, 0, pointData.data(), bufferDesc.size);
```

```{lit} C++, Create point buffer (hidden, for tangle root "Vanilla")
// Create point buffer
WGPUBufferDescriptor bufferDesc = WGPU_BUFFER_DESCRIPTOR_INIT;
bufferDesc.size = pointData.size() * sizeof(float);
bufferDesc.usage = WGPUBufferUsage_CopyDst | WGPUBufferUsage_Vertex; // Vertex usage here!
m_pointBuffer = wgpuDeviceCreateBuffer(m_device, &bufferDesc);

// Upload geometry data to the buffer
wgpuQueueWriteBuffer(m_queue, m_pointBuffer, 0, pointData.data(), bufferDesc.size);
```
````

In the list of **application attributes**, we replace `m_vertexBuffer` with `m_pointBuffer` and `m_indexBuffer`, and replace `m_vertexCount` with `m_indexCount`.

````{tab} With webgpu.hpp
```{lit} C++, Application attributes (append)
private: // Application attributes
	wgpu::Buffer m_pointBuffer = nullptr;
	wgpu::Buffer m_indexBuffer = nullptr;
	uint32_t m_indexCount = 0;
```
````

````{tab} Vanilla webgpu.h
```{lit} C++, Application attributes (append, for tangle root "Vanilla")
private: // Application attributes
	WGPUBuffer m_pointBuffer = nullptr;
	WGPUBuffer m_indexBuffer = nullptr;
	uint32_t m_indexCount = 0;
```
````

And as usual, we release buffers in `Terminate()`

````{tab} With webgpu.hpp
```{lit} C++, Terminate (prepend)
m_pointBuffer.release();
m_indexBuffer.release();
```
````

````{tab} Vanilla webgpu.h
```{lit} C++, Terminate (prepend, for tangle root "Vanilla")
wgpuBufferRelease(m_pointBuffer);
wgpuBufferRelease(m_indexBuffer);
```
````

```{lit} C++, Create point buffer (hidden, append, also for tangle root "Vanilla")
// It is not easy with the auto-generation of code to remove the previously
// defined `vertexBuffer` attribute, but at the same time some compilers
// (rightfully) complain if we do not use it. This is a hack to mark the
// variable as used and have automated build tests pass.
(void)m_vertexBuffer;
(void)m_vertexCount;
```

Buffer creation
---------------

Of course the **index data** must be stored in a **GPU-side buffer**. This buffer needs a usage of `BufferUsage::Index`.

````{tab} With webgpu.hpp
```{lit} C++, Create index buffer
// Create index buffer
// (we reuse the bufferDesc initialized for the vertexBuffer)
bufferDesc.size = indexData.size() * sizeof(uint16_t);
{{Fix buffer size}}
bufferDesc.usage = BufferUsage::CopyDst | BufferUsage::Index;
m_indexBuffer = m_device.createBuffer(bufferDesc);

m_queue.writeBuffer(m_indexBuffer, 0, indexData.data(), bufferDesc.size);
```
````

````{tab} Vanilla webgpu.h
```{lit} C++, Create index buffer (for tangle root "Vanilla")
// Create index buffer
// (we reuse the bufferDesc initialized for the vertexBuffer)
bufferDesc.size = indexData.size() * sizeof(uint16_t);
{{Fix buffer size}}
bufferDesc.usage = WGPUBufferUsage_CopyDst | WGPUBufferUsage_Index;;
m_indexBuffer = wgpuDeviceCreateBuffer(m_device, &bufferDesc);

wgpuQueueWriteBuffer(m_queue, m_indexBuffer, 0, indexData.data(), bufferDesc.size);
```
````

````{important}
A `writeBuffer` operation must copy a number of bytes that is a **multiple of 4**. To ensure this, we must **ceil the buffer size** up to the next multiple of 4 before creating it:

```{lit} C++, Fix buffer size (also for tangle root "Vanilla")
bufferDesc.size = (bufferDesc.size + 3) & ~3; // round up to the next multiple of 4
```

This means that we must also make sure that `indexData.size()` is a multiple of 2 (because `sizeof(uint16_t)` is 2):

```{lit} C++, Fix buffer size (append, also for tangle root "Vanilla")
indexData.resize((indexData.size() + 1) & ~1); // round up to the next multiple of 2
```
````

Render pass
-----------

To draw with an index buffer, there are **two changes** in the render pass encoding:

 1. Set the active index buffer with `renderPass.setIndexBuffer`.
 2. Replace `draw()` with `drawIndexed()`.

````{tab} With webgpu.hpp
```{lit} C++, Use Render Pass (replace)
renderPass.setPipeline(m_pipeline);

// Set both vertex and index buffers
renderPass.setVertexBuffer(0, m_pointBuffer, 0, m_pointBuffer.getSize());
// The second argument must correspond to the choice of uint16_t or uint32_t
// we've done when creating the index buffer.
renderPass.setIndexBuffer(m_indexBuffer, IndexFormat::Uint16, 0, m_indexBuffer.getSize());

// Replace `draw()` with `drawIndexed()` and `m_vertexCount` with `m_indexCount`
// The extra argument is an offset within the index buffer.
renderPass.drawIndexed(m_indexCount, 1, 0, 0, 0);
```
````

````{tab} Vanilla webgpu.h
```{lit} C++, Use Render Pass (replace, for tangle root "Vanilla")
wgpuRenderPassEncoderSetPipeline(renderPass, m_pipeline);

// Set both vertex and index buffers
wgpuRenderPassEncoderSetVertexBuffer(renderPass, 0, m_pointBuffer, 0, wgpuBufferGetSize(m_pointBuffer));
// The second argument must correspond to the choice of uint16_t or uint32_t
// we've done when creating the index buffer.
wgpuRenderPassEncoderSetIndexBuffer(renderPass, m_indexBuffer, WGPUIndexFormat_Uint16, 0, wgpuBufferGetSize(m_indexBuffer));

// Replace `draw()` with `drawIndexed()` and `m_vertexCount` with `m_indexCount`
// The extra argument is an offset within the index buffer.
wgpuRenderPassEncoderDrawIndexed(renderPass, m_indexCount, 1, 0, 0, 0);
```
````

We now see our "square", with color highlighting how the red and blue points ($A$ and $C$) are shared by both triangles:

```{figure} /images/index-buffer/deformed-quad.png
:align: center
:class: with-shadow
The square is deformed because of our window's aspect ratio.
```

Ratio correction
----------------

The square we obtained is deformed because its coordinates are expressed **relative to the window's dimensions**. This can be fixed by multiplying one of the coordinates by the ratio of the window (which is $640/480$ in our case).

We could do this either in the initial vertex data vector, but this will require is to update these values whenever the window dimension changes. A more interesting option is to use the **power of the vertex shader**:

```{lit} rust, Vertex shader position (also for tangle root "Vanilla")
// In vs_main():
let ratio = 640.0 / 480.0; // The width and height of the target surface
out.position = vec4f(in.position.x, in.position.y * ratio, 0.0, 1.0);
```

```{lit} rust, Vertex shader body (replace, hidden, also for tangle root "Vanilla")
var out: VertexOutput; // create the output struct
{{Vertex shader position}}
out.color = in.color; // forward the color attribute to the fragment shader
return out;
```

Although basic, this is a first step towards what will be the key use of the vertex shader when introducing **3D transforms**.

```{note}
It might feel a little unsatisfying to hard-code the window resolution in the shader like this, but we will quickly see how to make this more flexible thanks to uniforms.
```

```{figure} /images/index-buffer/quad.png
:align: center
:class: with-shadow
The expected square
```

Conclusion
----------

Using an index buffer is a rather **simple concept** in the end, and can save a lot of VRAM (GPU memory).

Additionally, it corresponds to the way traditional formats usually encode 3D meshes in order to **keep the connectivity information**, which is important for **authoring** (so that when the user moves a points all triangles that share this point are affected).

Note however that this only works when the common vertices **share all their attributes**. Two vertices that have the **same position but a different color** are still considered as **different points**, because the vertex shader must run for each of them individually.

````{tab} With webgpu.hpp
*Resulting code:* [`step034-next`](https://github.com/eliemichel/LearnWebGPU-Code/tree/step034-next)
````

````{tab} Vanilla webgpu.h
*Resulting code:* [`step034-vanilla-next`](https://github.com/eliemichel/LearnWebGPU-Code/tree/step034-vanilla-next)
````


