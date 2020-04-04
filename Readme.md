# Svelte Native Game Catalog

This is a simple Android app that uses the largest open videogame database <a href='https://api.rawg.io/docs/'>RAWG</a> to fetch and display more than 350,000 games. It is build using <a href='https://svelte-native.technology/'>svelte-native</a>, a minimalistic and concise javascript compiler for native development. Svelte-native again builds on <a href='https://www.nativescript.org/'>Nativescript</a>, so in order to build the project you will need to go through their setup guide and install the Telerik cli tools. 

## Setup and build
To test the project, run
```html

npm install
tns run android OR tns preview

```
## Project structure
### App.svelte 
...is the main entrance to the project. This file 
- fetches game-data on mount
- shows an initial scrollable list of game titles and game image using listView and Template.
- has a search function that searches for games by name
- displays a page number, a "next" button and a "previous" button.

### ./modals/GameInfoModal.svelte
GameInfoModal is a component shown via svelte-native's showModal() function. It is a simple article display that
- displays more detailed information about the game such as genres, age rating and compatible platforms, which is sent from the main component as a prop

- uses closeModal() to get back to the main screen 

   