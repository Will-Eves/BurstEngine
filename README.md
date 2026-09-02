# Burst Engine

**BurstEngine** is a **3D Game Engine** written in **C++**.

To use the engine, follow these steps:

1. Download the repository to your desired project folder
2. Install the necessary libraries (found in the [Dependencies](#dependencies) section of the README)
3. Begin writing your game in the main.cpp file!

## Quick Nav

[Dependencies](#dependencies)

[Quick Start](#quick-start)

[Load Assets](#load-assets)

## Dependencies

There are a few libraries that this project depends on. Some are already included in the `libs` folder, and some must be downloaded into the folder.

Included libraries:

1. [miniaudio.h](https://github.com/mackron/miniaudio)
2. [obj_loader.h](https://github.com/Bly7/OBJ-Loader/tree/master)
3. [stb_image.h](https://github.com/nothings/stb/tree/master)

Libraries you must install + instructions:

### GLEW

### GLFW

### GLM

## Quick Start

The basic skeleton of a Burst Engine project should look like this:

```cpp
#include <burst.h>

int main(){
    Burst::App::Setup(
        800,   // window width
        450,   // window height
        "Name" // window name
    );

    // load all assets (shaders, meshes, sounds, etc.)

    // create all entities (camera, objects, lights, managers, etc.)

    // now, run the program
    Burst::App::Run();
}
```

## Load Assets

All assets are loaded using the `Burst::AssetManager` namespace. When assets are loaded, they are loaded into pointers. There are a few different types of assets:

1. Texture
2. Material
3. Shader
4. Mesh
5. Sound

Textures are loaded like this:

```cpp
Burst::Texture* texture = Burst::AssetManager::Load<Burst::Texture>("path/to/texture.jpg");
```

Materials are loaded like this:

```cpp
Burst::Material* material = Burst::AssetManager::Load<Burst::Material>("!");

material->albedoTexture = texture;                   // sets the albedo texture of the material
material->textureTiling = Burst::Vector2(1.0, 1.0);  // sets the tiling of the material texturing

material->roughnessTexture = roughnessTexture;       // sets the roughness texture of the material

material->metallicTexture = metallicTexture;         // sets the metallic texture of the material

material->emissive = Burst::Color(1.0f, 0.0f, 0.0f); // sets the emissive color of the material
material->emissiveStrength = 1.0f;                   // sets the emissive strength of the material
material->emissiveTexture = emissiveTexture;         // sets the emissive texture of the material
```

Shaders are loaded like this:

```cpp
Burst::Shader* shader = Burst::AssetManager::Load<Burst::Shader>("path/to/vertex.glsl", "path/to/fragment.glsl");

shader->Bind(); // use this shader in the program
```

Meshes are loaded like this:

```cpp
Burst::Mesh* mesh = Burst::AssetManager::Load<Burst::Mesh>("path/to/mesh.obj");
```

Sounds are loaded like this:

```cpp
Burst::Sound* sound = Burst::AssetManager::Load<Burst::Sound>("path/to/sound.wav");
```
