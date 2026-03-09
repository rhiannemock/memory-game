# The memory game for the periodic table and elements.

# User story

This would be used to help students learn the elements and match them to their chemical symbol in the periodic table.

The game will consist of 3 levels.

The game will give a score.

The game will give feedback at the end of each round.

The website will consist of 3 pages.

index/home page explaining what the site is for.

rules page explaining how to use the game and how it works.

game page where you will play the game.

# javascript
we start the javascript with a dictionary of arrays with key/value pairs for each element.
eg

const periodicTable = [
  { symbol:"H", name: "Hydrogen", atomicNumber: 1, atomicMass: 1.008, category: "nonmetal" },
  { symbol:"He", name: "Helium", atomicNumber: 2, atomicMass: 4.0026, category: "noble gas" },
  etc
  
We then move on to setting variables for the code

let currentLevel = 1;
let score = 0;
let elementsInPlay = [];
let results = []; // Tracks { name, status }
let targetElement = null;
let questionQueue = [];

then link to certain parts of the html start button, results page for the user to interact and information to be displayed

const grid = document.getElementById('card-grid');
const startBtn = document.getElementById('start-btn');
const gameUi = document.getElementById('game-ui');
const targetDisplay = document.getElementById('target-name');
const summaryScreen = document.getElementById('summary-screen');
const summaryContent = document.getElementById('summary-content');

next comes our first function which will be initiated when the start button is pressed

function initLevel() {
    grid.innerHTML = '';
    summaryScreen.style.display = 'none';
    grid.style.display = 'grid';
    results = [];
    
    const shuffled = [...periodicTable].sort(() => 0.5 - Math.random());
    elementsInPlay = shuffled.slice(0, levels[currentLevel]);
    questionQueue = [...elementsInPlay].sort(() => 0.5 - Math.random());
    
this initiates the first level and displays items random item/cardss in a grid it hides the display and produces the amount of cards based on getting the level, it randomises the questions and displays them one at a time.

elementsInPlay.forEach(el => {
        const card = document.createElement('div');
        // We add the category as a class (replacing spaces with dashes)
        const categoryClass = el.category.replace(/\s+/g, '-');
        card.className = `element-card ${categoryClass}`;
        card.id = `card-${el.symbol}`;
        
        // Build the card with all data, but hidden via CSS
        card.innerHTML = `
            <div class="atomic-num-top">${el.atomicNumber}</div>
            <div class="symbol">${el.symbol}</div>
            <div class="details">
                <div class="el-name">${el.name}</div>
                <div class="el-mass">${el.atomicMass}</div>
                <div class="el-cat">${el.category}</div>
            </div>`;
            
        card.addEventListener('click', () => handleGuess(el, card));
        grid.appendChild(card);
    });

    nextQuestion();
}

the above code creates the card styling for each element and hides information on the card i had to replace spaces with dashes because the css classes cannot contain spaces.
there is then an event listener which waits for a click  which will then use the handleGuess function to decide what to do then calls the next question function.

function nextQuestion() {
    if (questionQueue.length === 0) {
        showSummary();
        return;
    }
    targetElement = questionQueue.shift();
    targetDisplay.innerText = `Find: ${targetElement.name}`;
}

this nextQuestion function checks the length of the questionQueue if it = 0 it will move onto the showsummary function and return results, if not it will shift the previous question off the list effectively moving to the next one.

function handleGuess(el, card) {
    if (!targetElement || card.classList.contains('correct')) return;

    if (el.symbol === targetElement.symbol) {
        card.classList.add('correct');
        score += 10;
        results.push({ name: targetElement.name, status: 'known' });
        document.getElementById('score').innerText = `Score: ${score}`;
        nextQuestion();
    } else {
        card.classList.add('wrong');
        results.push({ name: targetElement.name, status: 'missed' });

        this adds 10 points for a correct answer to the score and returns/logs correct as an answer if its wrong it logs missed

        // Auto-skip after red flash
        const currentTarget = targetElement;
        targetElement = null; 
        setTimeout(() => {
            card.classList.remove('wrong');
            nextQuestion();
        }, 600);
    }
}

this skips if the answer is wrong as my program was getting stuck here so it makes it move onto the next question.

function showSummary() {
    grid.style.display = 'none';
    summaryScreen.style.display = 'block';
    targetDisplay.innerText = "Level Complete!";
    
    summaryContent.innerHTML = results.map(res => `
        <div class="summary-item ${res.status}">
            <span>${res.name}</span>
            <span>${res.status.toUpperCase()}</span>
        </div>
    `).join('');

    this shows the summary of the answers given in the previous round 

    const continueBtn = document.getElementById('continue-btn');
    if (currentLevel >= 3) {
        continueBtn.innerText = "Play Again from Start";
        continueBtn.onclick = resetGame;
    } else {
        continueBtn.innerText = "Proceed to Next Level";
        continueBtn.onclick = () => { currentLevel++; initLevel(); };
    }
}

this gives a button to continue to the next round when the user is ready, it moves you onto the next level continueBtn.onclick = () => { currentLevel++; initLevel(); }; or a rest function can be chosen

function resetGame() {
    currentLevel = 1;
    score = 0;
    document.getElementById('score').innerText = "Score: 0";
    summaryScreen.style.display = 'none';
    gameUi.style.display = 'none';
    startBtn.style.display = 'inline-block';
    grid.innerHTML = '';
}

this offers a reset button if the user wants to strat the game again

startBtn.addEventListener('click', () => {
    startBtn.style.display = 'none';
    gameUi.style.display = 'block';
    initLevel();
});

document.getElementById('reset-btn').addEventListener('click', resetGame);

these are the event listeners for start button and reset button
# Acknowledgements

[periodic table image](https://www.curiousminds.co.uk/blogs/curious-mind-blog/what-is-the-periodic-table-of-chemical-elements)

google fonts
Roboto mono
I wanted an old school computer game kind of look with neon colours.

## Deployment


Set up a Github repository.

create a folder on your computer to save the files.

open visual studio code and connect to the github repository using the url github provides use the nominated folder to store your website files.

create first files and folders in visual studio and commit to the repository.

To activate github pages
 
Go to GitHub: Open your repository in the web browser.

Click the Settings tab, then find Pages in the left sidebar.

Under "Build and deployment," change the Source dropdown to "Deploy from a branch."

For the Branch option, choose main (or master) and select the root folder (/). Click Save.


GitHub will now start building your site.

Wait for a minute minute. Refresh the Page.

A banner will appear at the top with your live URL (e.g., https://username.github.io/my-site/). Your site is live!

# TESTING
I have a list of ways of testing below that i will be using, these will be completed, changes made and final testing done once all changes have been made.

## Tests

1. responsiveness testing
2. wave testing
3. HTML and CSS testing using w3c and jigsaw
4. link testing page by page
5. JS lint testing

   
   Once these tests are complete i will have a list of errors which will be worked on before the final testing phase.

1, I completed responsiveness testing before and during gameplay, the first test showed my cards overlapped when revealing the correct card details, altered the font size for mobile screen.
results as below:
![responsiveness testing 1](https://rhiannemock.github.io/memory-game/assets/testing/responsive/responsive1.png)
![responsiveness testing 2](https://rhiannemock.github.io/memory-game/assets/testing/responsive/responsive2.png)
![responsiveness testing 3](https://rhiannemock.github.io/memory-game/assets/testing/responsive/responsive3.png)
![responsiveness testing 4](https://rhiannemock.github.io/memory-game/assets/testing/responsive/responsive4.png)
![responsiveness testing 5](https://rhiannemock.github.io/memory-game/assets/testing/responsive/responsive5.png)
![responsiveness testing 6](https://rhiannemock.github.io/memory-game/assets/testing/responsive/responsive6.png)
![responsiveness testing 7](https://rhiannemock.github.io/memory-game/assets/testing/responsive/responsive7.png)
![responsiveness testing 8](https://rhiannemock.github.io/memory-game/assets/testing/responsive/responsive8.png)
![responsiveness testing 9](https://rhiannemock.github.io/memory-game/assets/testing/responsive/responsive9.png)


2, wave testing
   p color changed from purple to pale blue, purple provided low contrast.

3, w3c testing
a closing tag and an aria label issue.
h2 tag empty because it is filled with javascript
![w3c testing 1](https://rhiannemock.github.io/memory-game/assets/testing/w3c/w3c1.png)
![w3c testing 2](https://rhiannemock.github.io/memory-game/assets/testing/w3c/w3c2.png)
![w3c testing 3](https://rhiannemock.github.io/memory-game/assets/testing/w3c/w3c3.png)
![w3c testing 4](https://rhiannemock.github.io/memory-game/assets/testing/w3c/w3c4.png)
![w3c testing 5](https://rhiannemock.github.io/memory-game/assets/testing/w3c/w3c5.png)
![w3c testing 6](https://rhiannemock.github.io/memory-game/assets/testing/w3c/w3c6.png)

jigsaw testing
![jigsaw testing 1](https://rhiannemock.github.io/memory-game/assets/testing/jigsaw/jigsaw.png)

js validator
![js validator](https://rhiannemock.github.io/memory-game/assets/testing/js-validator/js-validator.png)

overall it was an interesting project but i think i would have chosen something different if doing it again, the key is to think about what and how it will work to make it the best it can be.
