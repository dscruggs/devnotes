# Programming terminology notes

There's a lot of terms out there and I want a quick sheet to track working definitions for some of the more commonly used ones.

### Bindings

`Bindings` is just an interface to link two things. Usually refers to the associations or connections that link a name, input, or interface to its corresponding value, function, or resource, often serving as a bridge between components, systems, or languages.

Typical usage is when describing multi-language applications and a binding allows one programming language to use code or APIs written in another. Very common in python with a lot of their libraries written in C/C++

Other Examples:

- **data bindings** - connecting UI components to data sources or models (react state management for example)
- **systems programming** - connection between system-level APIs and higher level libraries, such as OpenGL bindings for Python or Javascript (WebGL).
- **Single Programming languages** - Bindings usually refer to the  Have static bindings that are determined at compile time like function overloading in C++, or dynamic binding at runtime like polymorphism in OOP

### Kernel

At a high level, a `kernel` is the central or core component of a system, algorithm, or framework. It performs the most fundamental operations and manages essential resources or processes. It often acts as the intermediary between lower-level and higher-level components, facilitating their interaction.  

Most commonly used when referring to **operating systems**, where the kernel is responsible for managing system resources and facilitating communication between hardware and software.  
Functions include:

- Process and thread management.
- Memory management.
- Device driver management.
- System call handling.

Other uses:

- **machine learning algorithms** - mathematical function used as part of the inference or training
- **image and signal processing** - small matrix or filter used in convolutions or transformations
- **Pytorch or other libraries** - core code that optimizes various algorithms for specific hardware
- **databases** - part of the database management code that handles storage, retrieval, etc.

### Polymorphism

`Polymorphism` is the concept of presenting the same interface for varying underlying data types of functions. Python handles this implicitly

### Runtime

`Runtime` refers to the period or environment in which a program or code executes, encompassing all the tools, libraries, and infrastructure needed for its execution and management.

Runtime systems or environments have all the libraries, tools, and infra to execute a program, acting as a bridge from application to the OS and hardware (memory management, exception handling, type checking, interpreters, setup and teardown, etc.)

**Examples:**

- python interpreter
- Tensorflow, ONNX, CUDA runtimes for executing ML programs
- managed cloud environments for things like AWS lambda
