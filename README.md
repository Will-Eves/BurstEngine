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

[Particle System](#particle-system)

[Getting User Input](#getting-user-input)

[Time Management](#time-management)

[Math Systems](#math-systems)

[Misc Systems](#misc-systems)

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

Components can access their parent entity through `this->parent`.

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

## Particle System

Particle systems are handled by the `Burst::ParticleSystem` component. However, on its own, this component cannot do anything.

Everything in particle systems is done through `Burst::ParticleComponent` instances. The basic skeleton of a particle component looks like this:

```cpp
struct MyParticleComponent : Burst::ParticleComponent{
    virtual void Update(std::vector<Particle*>* particles){
        // modify particles here
    }
};
```

Add a particle component to a particle system is done like this:

```cpp
particleSystem->AddComponent<MyParticleComponent>();
```

There are a few particle components included in the engine already:

1. ParticleSpawner
2. ParticleGravity
3. ParticleScaler
4. ParticleMeshRenderer

The `Burst::ParticleSpawner` component is used like this:

```cpp
particleSystem->AddComponent<Burst::ParticleSpawner>(
    0.1f, // time in seconds between particle spawn
    5.0f, // time in seconds that a particle lives

    Burst::RandomVector3(
        Burst::Vector3(-0.5f, -0.25f, -0.5f),
        Burst::Vector3(0.5f, 0.25f, 0.5f)
    ), // the starting velocity range of the particles

    Burst::RandomVector3(
        Burst::Vector3(-3.0f, -3.0f, 0.0f),
        Burst::Vector3( 3.0f,  3.0f, 0.0f)
    ) // the starting angular velocity range of the particles
);
```

The `Burst::ParticleGravity` component is used like this:

```cpp
particleSystem->AddComponent<Burst::ParticleGravity>(
    Burst::Vector3(0.0f, -9.81f, 0.0f) // the gravity force applied to the particles
);
```

The `Burst::ParticleScaler` component is used like this:

```cpp
particleSystem->AddComponent<Burst::ParticleScaler>(
    Burst::RangeVector3(
        Burst::Vector3(0.05f, 0.05f, 0.05f), // start scale
        Burst::Vector3(0.0f,  0.0f,  0.0f)   // end scale
    ) // the range vector used to scale the particles over time
);
```

The `Burst::ParticleMeshRenderer` component is used like this:

```cpp
particleSystem->AddComponent<Burst::ParticleMeshRenderer>(
    mesh,     // the Burst::Mesh to be used
    material, // the Burst::Material to be used
);
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
Controllers have a few different variables that determine input. They are used like this:

```cpp
Burst::Vector2 leftStick = controller->axes[0]; // retrieves the first joystick axis from the controller

if(controller->buttons[GLFW_GAMEPAD_BUTTON_A]){
    // A button is currently down
}
// see GLFW website for more controller binds

Burst::Vector2 leftHat = controller->hats[0]; // retrieves the first hat axis from the controller
```

## Time Management

Time management is done through the `Burst::Time` namespace. Time is handled through `events` which are handled through the time namespace.

Events are handled like this:

```cpp
// starts a new event 
Burst::Time::StartEvent(
    "Test Event" // event name
);

// returns the events current time, and then resets the events time to 0.0
float eventTime = Burst::Time::StampEvent("Test Event");

// returns the events current time
float eventTime = Burst::Time::SnapEvent("Test Event");

// returns the events current time and stops the event
float eventTime = Burst::Time::EndEvent("Test Event");
```

There are two baseline events:

1. Time
2. DeltaTime

The `Time` event returns the time since the game was started. It can also be accessed using `Burst::Time::programTime`.

The `DeltaTime` event returns the time since the last frame. It can also be accessed using `Burst::Time::deltaTime`.

*Important Notice*: Please don't use `StampEvent` on either of these events because they will no longer work properly.

## Math Systems

The math system contains vector math as well as random number generation.

There are four types of vectors in the engine:

1. Vector2
2. Vector3
3. RangeVector3
4. RandomVector3

The `Burst::Vector2` stores two variables, `x` and `y`. Likewise, the `Burst::Vector3` stores three variables, `x`, `y`, and `z`.

Vector math can be done using normal operators like this:

```cpp
Burst::Vector2 a = Burst::Vector2(2.0f, 3.0f);
Burst::Vector2 b = Burst::Vector2(0.5f, 2.0f);

// basic operators between Vector2s
Burst::Vector2 addResult = a + b;      // result: x = 2.5f, y = 5.0f
Burst::Vector2 subtractResult = a - b; // result: x = 1.5f, y = 1.0f
Burst::Vector2 multiplyResult = a * b; // result: x = 1.0f, y = 6.0f
Burst::Vector2 divideResult = a / b;   // result: x = 4.0f, y = 1.5f

// compound assignment operators between Vector2s
a += b; // a: x = 2.5f, y = 5.0f
a -= b; // a: x = 1.5f, y = 1.0f
a *= b; // a: x = 1.0f, y = 6.0f
a /= b; // a: x = 4.0f, y = 1.5f

// basic operators between Vector2 and float
Burst::Vector2 addResult = a + 1.0f;      // result: x = 3.0f, y = 4.0f
Burst::Vector2 subtractResult = a - 1.0f; // result: x = 1.0f, y = 2.0f
Burst::Vector2 multiplyResult = a * 2.0f; // result: x = 4.0f, y = 6.0f
Burst::Vector2 divideResult = a / 2.0f;   // result: x = 1.0f, y = 1.5f

// compound assignment operators between Vector2 and float
a += 1.0f; // result: x = 3.0f, y = 4.0f
a -= 1.0f; // result: x = 1.0f, y = 2.0f
a *= 2.0f; // result: x = 4.0f, y = 6.0f
a /= 2.0f; // result: x = 1.0f, y = 1.5f
```

Vector components can be accessed like this:

```cpp
Burst::Vector2 vector = Burst::Vector2(1.0f, 2.0f);

float x = vector.x;
float y = vector.y;
```

### RangeVector3

The `Burst::RangeVector3` class contains two `Burst::Vector3` instances, and returns a value between them based on the float value provided.

This is done like this:

```cpp
Burst::RangeVector3 rangeVector = Burst::RangeVector3(
    Burst::Vector3(0.0f, 0.0f, 0.0f), // the value at 0.0f
    Burst::Vector3(1.0f, 2.0f, 3.0f)  // the value at 1.0f
);

Burst::Vector3 middleValue = rangeVector.GetVector3(0.5f); // result: x = 0.5f, y = 1.0f, z = 1.5f
```

### RandomVector3

The `Burst::RandomVector3` class contains two `Burst::Vector3` instances, and returns a random value between.

This is done like this:

```cpp
Burst::RandomVector3 randomVector = Burst::RandomVector3(
    Burst::Vector3(0.0f, 0.0f, 0.0f), // the minimum values
    Burst::Vector3(1.0f, 2.0f, 3.0f)  // the maximum values
);

Burst::Vector3 result = randomVector.GetVector3(); // result: x = 0.0f-1.0f, y = 0.0f-2.0f, z = 0.0f-3.0f
```

### Random Numbers

Random numbers are accessed through the `Burst::Random` namespace.

The random number generator can be seeded like this:

```cpp
Burst::Random::Seed(
    567 // the seed of the generator
);
```

Random numbers can be generated like this:

```cpp
float randomFloat = Burst::Random::Range<float>(
    0.0f, // minimum value
    1.0f, // maximum value
); // result: between 0.0f and 1.0f

double randomDouble = Burst::Random::Range<double>(-2.0, 2.0); // result: between -2.0 and 2.0

int randomInt = Burst::Random::Range<int>(0, 100); // result: between 0 and 100
```

## Misc Systems

There are a few remaining systems that do not deserve their own entire modules, so they are covered here. They are:

1. [The Color Class](#the-color-class)

### The Color Class

The `Burst::Color` class contains four variables, `r` (red value), `g` (green value), `b` (blue value), and `a` (alpha value). All are in the range from 0.0f to 1.0f.

It is used like this:

```cpp
Burst::Color color = Burst::Color(
    0.75f, // the red value
    0.25f, // the green value
    0.1f,  // the blue value
    0.8f   // the alpha value
);
```
