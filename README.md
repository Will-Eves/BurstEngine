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

[Using The Entity Component System](#using-the-entity-component-system)

[Using Components](#using-components)

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

## Using The Entity Component System

The entity component system consists of 3 main systems:

1. Components
2. Entities
3. The Scene

There are a few components included in the engine already, which can be found in the [Using Components](#using-components) section. Creating your own component can be done like this:

```cpp
struct MyComponent : Burst::Component{
    virtual void Start(){
        // called after Burst::App::Run() is called, or when the parent entity is created
    }

    virtual void Update(){
        // runs every frame, before rendering
    }

    virtual void Destroy(){
        // runs when the parent Entity is destroyed
    }
};
```

There are a few components built into the engine that can be found in the [Using Components](#using-components) section of the documentation.

Entities are the next part of the system. Components sit on top of entities. The entities themselves are added to what is called the `Burst::Scene`. Creating an entity within the scene is done like this:

```cpp
Burst::Entity* entity = Burst::Scene::AddEntity("Entity Name");

entity->position = Burst::Vector3(0.0f, 5.0f, 0.0f);   // sets the position of the entity
entity->rotation = Burst::Vector3(0.0f, 180.0f, 0.0f); // sets the rotation of the entity
entity->scale    = Burst::Vector3(2.0f, 0.5f, 2.0f);   // sets the scale of the entity

MyComponent* myComponent1 = entity->AddComponent<MyComponent>(); // adds the MyComponent component to the entity

entity->RemoveComponent(myComponent1); // remove the myComponent1 instance of MyComponent from the entity
```

*Important Notice: Capability for more than one scene will be added in the future.*

## Using Components

There are a few different built-in components in the engine currently. They include:

1. MeshRenderer
2. Camera
3. Light
4. AudioSource
5. SpriteRenderer
6. ParticleSystem

Their uses are documented in the following sections.

### MeshRenderer

Information!

### Camera

Information!

### Light

Information!

### AudioSource

Information!

### SpriteRenderer

Information!

### ParticleSystem

Information!
