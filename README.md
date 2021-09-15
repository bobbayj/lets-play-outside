# lets-play-outside

## Why I am stopping development on this project
It's been a while since I put any effort into this and after some deeper thinking, I believe it's time to call it quits.
Here are my reasons:
1. **Google has already won**: after asking around, it became more than obvious that Google Maps (or even search) is a quick way for people to find activities. Most people already have a pre-conceived idea of what they want to search for before they go searching. The fact that I wanted to add "filters" is basically a more convoluted way of searching directly in Google Maps.
2. **Development costs**: There were really just 2 options, Google Maps (premium, everyone uses it) or MapBox (the poor cousin of Google Maps with less support and locations). Sure, Mapbox offer a very basic free tier, but it didn't go far. And the technical debt I'd rack by getting deeper into Mapbox no longer seemed worth those dollars saved.
3. **Technical Limitations**: The tool depended heavily on what the map APIs allow. Mapbox was limited to searching a number of Places of Interest nearby a given coordinate, and so it would have required developing and maintaining a separate PoI database.

So, it is with this that I bid adieu to this project. It was a great idea while it lasted, but it's time to move on 

---

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
- Backend: don't need one yet?
  - *Recommendations welcome*

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
  - **Right**; prompt navigation
