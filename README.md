# lets-play-outside
Randomly pick something to do on a map

I am really rusty with Vue and front-end dev but will do my best.

Hold me accountable by buying me a coffee 😀

---

## Architecture
- Vue 3 frontend
    - Integrate with MapBox
- Backend: don't need one yet? *Recommendations welcome*

## User Journey

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
        - Close (<10km, 20min drive)
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
- Swipe left / right?
- Once they select, open navigation?