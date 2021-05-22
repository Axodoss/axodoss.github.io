## Notes on Game Engine Components

**High Level Components**
* **Asset System** - _responsible for loading and managing assets like fonts, textures, etc._
* **Rendering System** - _responsible for rendering the game world, all the 2D and 3D assets._
* **Audio System**
* **Physics System**
* **Interaction System**


**Low Level Components**
* **Thread Manager** - _responsible for the thread pool and memory exchange._
* **File Manager** - _responsible for file I/O including packaged files._

---

---

## Notes on Frame Rate Independent Update in Game Engines

```c
last_time = Time();
accumulated_time = 0;

while (1)
{
	current_time = Time();
	accumulated_time += current_time - last_time;
	last_time = current_time;

	while (accumulated_time >= FRAME_TIME)
	{
		UpdateGame();
		accumulated_time -= FRAME_TIME;
	}

	Render();
}
/*
     --------------------- Time ------------------>

    Scenario 1: UpdateGame() is called twice and a small gap of time is left
                resulting in a visual hack every few frames.
     ----------------------------------------------
     |   UpdateGame()   |   UpdateGame()   |      |
     ----------------------------------------------
     |                  Render()                  |
     ----------------------------------------------

	Scenario 2: UpdateGame() takes to long resulting in that 'accumulated_time' is 
	            larger and larger for each frame.
     --------------------------------------------------
     |                UpdateGame()                    | <-- UpdateGame() takes longer than FRAME_TIME
     --------------------------------------------------
     |                  Render()                  |
     ----------------------------------------------
*/
```

---

---

## Notes on Building an Streamable Asset System for Game Engines

---

### Interface of the Streamable Asset System

**Public Interface**
```c
/**
 * Allocates an AssetId and puts the asset on the loading queue.
 *
 * If the asset was already loaded and the asset type is allowed
 * to have multiple references then the referene counter is 
 * incrementet.
 *
 * 'params' can be any type and any number of parameters that
 * are required to load the asset. Either 'params' are part
 * of an asset manifest definition or provided to the function.
 * Parameters can be font size for font assets, or image width
 * and height for dynamic image assets - whatever the asset type
 * requires.
 *
 * 'AssetType' hits of what type the asset is. This can be used
 * to control which asset loader that should be used. If using
 * an asset manifest then this information could be put there.
 */
AssetId Load(AssetType type, string name, params...);

/**
 * Returns true if the asset has been fully loaded.
 */
bool IsReady(AssetId id);

/**
 * Decrements the reference counter on the asset.
 */
void Unload(AssetId id);
```

---

### Using Asset Manifest

Using a manifest file (or files) allows for a cleaner public interface but requires us to create a manifest of sort that contains all the nessecarry parameters to create the asset.

**manifest.json**
```json
[
	{
		"name": "font_luna_10",
		"type": "font",
		"resource": "fonts/luna.ttf",
		"parameters": {
			"size": 10,
			"bold": false
		}
	},
	{
		"name": "font_luna_15b",
		"type": "font",
		"resource": "fonts/luna.ttf",
		"parameters": {
			"size": 15,
			"bold": true
		}
	},
	{
		"name": "images/cloud",
		"type": "texture",
		"resource": "images/cloud.png"
	}
]
```

**Public Interface**
```c
/**
 * Allocates an AssetId and puts the asset on the loading queue.
 */
AssetId Load(string name);

bool IsReady(AssetId id);

void Unload(AssetId id);
```

---

### Private Interface of the Asset System
_TODO_

---