---
layout: default
title: Errata
---

## Errata

We've worked hard to make this book accurate and consistent with industry best practices, but errors inevitably slip through. If you spot one, we'd be grateful to hear about it.

To report an error in the book or a bug in the companion project, please [open an issue](https://github.com/PrinciplesGameAudioAndSoundDesign/PGASD-2ndEdition/issues) and include:
 
* The chapter, section, and page number.

* For code examples, the code block number on that page and the line number.

* For project bugs, your Unity version and operating system.

Confirmed corrections are listed below, sorted by chapter and page number.

---

### Chapter 7: Coding Basics for Game Audio
 
#### 7.5.1 Conditions and Selection Statements
 
- **Page 215, code example 1, line 6.** Declare `_timeOfDay` as `public` so it can be modified from the Inspector:
 
    ```csharp
    public int _timeOfDay = 0;
    ```
 
    This has already been fixed in the companion project.
 

#### 7.5.2 Loops
 
- **Page 219, code example 1, line 4.** The `float` literal is missing the `f` suffix. Replace the line with:
 
    ```csharp
    public float _pitchThreshold = 0.1f;
    ```
 
- **Page 223, code example 1, line 3.** When assigning a rounded `float` to an `int`, use `Mathf.RoundToInt()`. `Mathf.Round()` returns a `float`, which can't be implicitly converted to an `int`. Replace the line with:
 
    ```csharp
    int numIterations = Mathf.RoundToInt(duration / period);
    ```
 

#### 7.7 Coroutines
 
- **Page 250, code example 1, lines 12–16.** When you stop a coroutine with `StopCoroutine()`, it never reaches the end of its function. If the coroutine is guarded by a Boolean such as `_inCoroutine`, you must set that Boolean back to `false` manually. Otherwise, the coroutine can't be started again:
 
    ```csharp
    if (Input.GetKeyDown(KeyCode.Alpha0))
    {
        StopCoroutine(_myCoroutine);
        _source.Stop();
        _inCoroutine = false;
    }
    ```
 
#### 7.7.1 Fades and Transitions
 
- **Pages 253–254, `Fades` script, lines 27–58.** The `_inCoroutine` Boolean is declared and checked in `Update()`, but it is never set. Both `FadeIn()` and `FadeOut()` should set it to `true` when they start and back to `false` when they finish. `FadeOut()` should also stop playback once the volume reaches 0:
 
    ```csharp
    IEnumerator FadeIn(float duration, float targetVolume = 1.0f)
    {
        _inCoroutine = true;
    
        _source.volume = 0.0f;
        _source.Play();
    
        float timer = 0.0f;
        while (timer < duration)
        {
            _source.volume = Mathf.Lerp(0.0f, targetVolume, timer / duration);
    
            timer += Time.deltaTime;
            yield return null;
        }
    
        _source.volume = targetVolume;
    
        _inCoroutine = false;
    }
    
    IEnumerator FadeOut(float duration)
    {
        _inCoroutine = true;
    
        float initialVolume = _source.volume;
    
        float timer = 0.0f;
        while (timer < duration)
        {
            _source.volume = Mathf.Lerp(initialVolume, 0.0f, timer / duration);
    
            timer += Time.deltaTime;
            yield return null;
        }
    
        _source.volume = 0.0f;
        _source.Stop();
    
        _inCoroutine = false;
    }
    ```
    
    This has already been fixed in the companion project.
 
---
 
### Chapter 9: Implementing Audio—Common Scenarios
 
#### 9.5.2 LayerMasks and Avoiding the Pebble Effect
 
- **Pages 345–348, selecting `LayerMask` layers.** This isn't an error, but there's a simpler way to choose which layers a `LayerMask` includes: serialize a `LayerMask` field. Unity then shows a dropdown menu in the Inspector where you can select multiple layers.
 
    ```csharp
    [SerializeField] private LayerMask _layerMask;
    ```
    
    With this approach, the `LayerMask.GetMask()` call in `Start()` (line 29 of the `LayerMaskSmartSource` script, page 348) is no longer needed. The example in the companion project has been updated to use this workflow.
