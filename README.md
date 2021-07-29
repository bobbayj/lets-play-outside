# lets-play-outside

## Quick Summary
1. Have no ideas of what to do / where to go in your city
2. Use this webapp to help choose something to do...
3. Pick a general area to go...
4. and then receive suggestions (random or otherwise)

I am really rusty with Vue and front-end dev but will do my best.

Hold me accountable by buying me a coffee 😀 [![Donate](https://img.shields.io/badge/$-support-ff69b4.svg?style=flat)](https://paypal.me/bobjin)  

---

## Architecture
- Vue 3 frontend
  - Integrate with MapBox
- Backend: *Recommendations welcome*
  - Should be server-side processing for location search

## User Journey
*I should really put this into a note-taking app, like Notion. But this will do for now.*
<br><br>
- Basic logic tree to help you narrow options (8 permutations):
  - Q1: Where do you want to be?
    - Indoors
    - Outdoors
    - Don't care
  - Q2: What do you want to do?
      - Eat
    - Shop
    - Explore
  - Q3: Where do you want to go?
    - Close (less than 10km, 20min drive)
    - Far (20min to 1hr drive)
    - Road trip (1hr drive)
    - Anywhere
- Have filters to generate the next item once they start generating locations
  - Filter 1: What is your budget?
    - Feeling bougie
    - Money's tight
    - Whatever
  - Filter 2: Change what you want to do?
    - Eat
    - Shop
    - Explore
- Swipe...
  - **Left**; next suggestion + wiggle the filter button
  - **Right**; prompt navigation?

## To do
<br><br>
- Search for POI locations around user
- Filter 