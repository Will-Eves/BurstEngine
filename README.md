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

[Getting User Input](#getting-user-input)

[Time Management](#time-management)

[Particle System](#particle-system)

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

// write all your component code

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
3. Lights (DirectionalLight, AmbientLight, PointLight)
6. AudioSource
7. SpriteRenderer
8. ParticleSystem

Their uses are documented in the following sections.

### MeshRenderer

The `Burst::MeshRenderer` component is vital to rendering. Mesh renderers need a `Burst::Mesh` and a `Burst::Material` provided to them. A mesh renderer can be added to an entity like this:

```cpp
entity->AddComponent<Burst::MeshRenderer>(
    mesh,    // the Burst::Mesh to be used by the MeshRenderer
    material // the Burst::Material to be used by the MeshRenderer
);
```

### Camera

The `Burst::Camera` component is also vital to rendering. For anything to render to the screen, there must be an entity with a `Burst::Camera` component attached to it. A camera can be added to an entity like this:

```cpp
entity->AddComponent<Burst::Camera>(
    70.0f, // the cameras verticle fov
    0.01f, // the cameras near clip plane
    100.0f // the cameras far clip plane
);
```

### Lights

There are a 3 different types of lights:

1. DirectionalLight
2. AmbientLight
3. PointLight

Directional lights are created like this:

```cpp
Burst::DirectionalLight* light = lightEntity->AddComponent<Burst::DirectionalLight>();

light->color = Burst::Color(1.0f, 0.0f, 0.0f); // sets the color of the DirectionalLight
light->intensity = 1.0f;                       // sets the intensity of the DirectionalLight

/*
The position of the light entity determines the direction of the light.

This means that lighting is applied to each fragment as if the directional light was lightEntity->position away from the fragment (y position inverted).
*/
lightEntity->position = Burst::Vector3(-0.5f, -0.5f, -0.5f);
```

Ambient lights are created like this:

```cpp
Burst::AmbientLight* light = lightEntity->AddComponent<Burst::AmbientLight>();

light->color = Burst::Color(1.0f, 0.0f, 0.0f); // sets the color of the AmbientLight
light->intensity = 1.0f;                       // sets the intensity of the AmbientLight
```

Point lights are created like this:

```cpp
Burst::PointLight* light = lightEntity->AddComponent<Burst::PointLight>();

light->color = Burst::Color(1.0f, 0.0f, 0.0f); // sets the color of the PointLight
light->intensity = 1.0f;                       // sets the intensity of the PointLight
light->radius = 5.0f;                          // sets the radius of the PointLight (the furthest part the light reaches)

lightEntity->position = Burst::Vector3(10.0f, 2.0f, 0.0f); // set the position of the point light
```

### AudioSource

The `Burst::AudioSource` component is used to play sounds from a specific entity. It can be used like this:

```cpp
Burst::AudioSourece* audioSource = entity->AddComponent<Burst::AudioSource>(
    sound // the sound to be used by the audio source
);

audioSource->Play(); // play the sound
audioSource->Stop(); // stops the sound

audioSource->SetVolume(0.5f); // sets the volume of the sound
audioSource->SetPitch(1.5f);  // sets the pitch of the sound
```

*Alternatively* you can simply play sound from the `Burst::Sound` asset itself like this:

```cpp
sound->Play(); // play the sound
sound->Stop(); // stops the sound

sound->SetVolume(0.5f); // sets the volume of the sound
sound->SetPitch(1.5f);  // sets the pitch of the sound
```

### SpriteRenderer

The `Burst::SpriteRenderer` component is used to render 2D sprites to the screen. It can be used like this:

```cpp
Burst::SpriteRenderer* spriteRenderer = entity->AddComponent<Burst::SpriteRenderer>(
    texture // the texture rendered by the SpriteRenderer
);

spriteRenderer->color = Burst::Color(1.0f, 0.0f, 0.0f); // tints the sprite
```

### ParticleSystem

The `Burst::ParticleSystem` component is fairly complex and will be fully covered in the [Particle System](#particle-system) section of the documentation.

Adding a basic particle system to an entity looks like this:

```cpp
Burst::ParticleSystem* particleSystem = entity->AddComponent<Burst::ParticleSystem>();
```

## Getting User Input

All user input is done through the `Burst::Input` namespace. GLFW keybinds are used in all the functions.

### Keyboard Input

Key input is read like this:

```cpp
if(Burst::Input::GetKeyDown(GLFW_KEY_A)){
    // 'a' key is currently down (true as long as key is down)
}

if(Burst::Input::GetKeyPressed(GLFW_KEY_A)){
    // 'a' key was clicked (only true for the first frame the key is down)
}

if(Burst::Input::GetKeyUp(GLFW_KEY_A)){
    // 'a' key is currently up (true whenever the current key is not down)
}

if(Burst::Input::GetKeyReleased(GLFW_KEY_A)){
    // 'a' key released (only true for the first frame the key is up)
}
```

General WASD and Arrow Key input is read like this:

```cpp
double horizontalAxis = Burst::Input::GetHorizontal(); // returns a value from -1.0 to 1.0 depending on input
double verticleAxis = Burst::Input::GetVertical();     // returns a value from -1.0 to 1.0 depending on input
```

### Mouse Input

The mouse position is read like this:

```cpp
Burst::Vector2 mousePosition = Burst::Input::GetMousePosition();
```

Changing cursor properties is done like this:

```cpp
Burst::Input::HideCursor(); // disables mouse cursor graphics
Burst::Input::ShowCursor(); // enables mouse cursor graphics

Burst::Input::DisableCursor(); // locks the mouse cursor and hides it
Burst::Input::EnableCursor();  // unlocks the mouse cursor and shows it
```

Mouse button input is read like this:

```cpp
if(Burst::Input::MouseButtonDown(GLFW_MOUSE_BUTTON_LEFT)){
    // left click is currently down (true as long as the button is down)
}

if(Burst::Input::MouseButtonPressed(GLFW_MOUSE_BUTTON_LEFT)){
    // left click was cliked (only true for the first frame the button is down)
}

if(Burst::Input::MouseButtonUp(GLFW_MOUSE_BUTTON_LEFT)){
    // left click is currently up (true as long as the button is not down)
}

if(Burst::Input::MouseButtonReleased(GLFW_MOUSE_BUTTON_LEFT)){
    // left click was released (only true for the first frame the button is up)
}
```

### Controller Input

Controller input is managed through `Burst::Input::Controller` instances. The `Burst::Input` system manages these controllers automatically.

Controllers should be checked to see if they are connected before they are used. There are 16 available controllers (0-15). This is done like this:

```cpp
if(Burst::Input::GetControllerActive(0)){
    // controller 0 is active
}
```

Once a controller has been verified as connected, then you can access it like this:

```cpp
Burst::Input::Controller* controller = Burst::Input::GetController(0);
```
