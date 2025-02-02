<script>
    import { onMount } from "svelte";
    onMount(() => {
        if (window.location.href.includes("firstLoad")) {
            window.location.href = window.location.href.replace("firstLoad", "");
            // location.reload();
        }
    });
</script>
<div class="how-to-play">
    <div>
        <h1>How to Play 
            <a href="/#firstLoad">HyperPong</a></h1>
    </div>

    <p class="section">Welcome to HyperPong - a high-energy twist on the classic Pong game! Master the art of controlling 4 paddles simultaneously in this exciting arcade challenge.</p>

    <h2>Basic Controls:</h2>
    <div class="controls-section">
        <h3>Left Paddle:</h3>
        <ul>
            <li>W key: Move Up</li>
            <li>S key: Move Down</li>
        </ul>

        <h3>Right Paddle:</h3>
        <ul>
            <li>Up Arrow: Move Up</li>
            <li>Down Arrow: Move Down</li>
        </ul>

        <h3>Top Paddle:</h3>
        <ul>
            <li>A key: Move Left</li>
            <li>D key: Move Right</li>
        </ul>

        <h3>Bottom Paddle:</h3>
        <ul>
            <li>Left Arrow: Move Left</li>
            <li>Right Arrow: Move Right</li>
        </ul>
    </div>

    <h2>Getting Started:</h2>
    <div class="section">
        <ol>
            <li>Select your preferred difficulty level</li>
            <li>The ball will start moving from the center</li>
            <li>Use the controls above to move all paddles simultaneously</li>
            <li>Prevent the ball from passing your paddles</li>
            <li>Each successful deflection increases your score</li>
            <li>The ball speeds up over time for extra challenge</li>
            <li>Game ends if you miss the ball with any paddle</li>
        </ol>
    </div>

    <h2>How It Works:</h2>
    <div class="section">
        <p>HyperPong runs on a dynamic game loop that continuously updates paddle positions and ball movement. The ball's velocity increases gradually over time, making each game progressively more challenging. The game tracks collisions between the ball and paddles, updating the score and playing sound effects for each successful deflection. Your high score is saved locally so you can track your improvement over time.</p>
    </div>

    <h2>Game Features:</h2>
    <div class="section">
        <ul>
            <li>Progressive difficulty: Increasing ball speed</li>
            <li>Score tracking: Real-time point system</li>
            <li>Difficulty levels: Multiple options to choose from</li>
            <li>Sound effects: Engaging audio feedback</li>
            <li>High score: Local storage tracking</li>
        </ul>
    </div>

    <h2>Pro Tips:</h2>
    <div class="section">
        <ul>
            <li>Beginner tip: Start with slower difficulties</li>
            <li>Focus tip: Keep your eyes on the ball's trajectory</li>
            <li>Strategy tip: Position your paddles proactively</li>
            <li>Practice tip: Each game improves coordination</li>
            <li>Challenge tip: Aim to beat your high score</li>
        </ul>
    </div>

    <h2>Challenge Yourself:</h2>
    <div class="section">
        <p>Can you master the art of controlling all four paddles? How long can you keep the ball in play as it gets faster and faster? Jump in and find out!</p>
    </div>

    <div class="footer-note">
        <p>Remember: Quick reflexes and good coordination are key to achieving high scores in HyperPong. Good luck!</p>
    </div>
</div>

<style>
    * {
        text-shadow: 0px 4px 2px rgba(255, 255, 255, 0.2);
        
        font-family: "Silkscreen", serif;
        font-weight: 400;
        font-style: normal;
        

    }
    :global(:root) {
        --white: #ffffffff;
    --picton-blue: #00a7e1ff;
    --rich-black: #00171fff;
    --prussian-blue: #003459ff;
    --cerulean: #007ea7ff;
    }
    
    :global(html, body) {
        margin: 0px;
        padding: 0px;
    }

    .how-to-play {
        padding-top: 1em;
    }
    h1 {
        font-size: xx-large;
    }
    h2 {
        font-size: x-large;
    }

    h1, h2, h3, a {
        font-weight: bolder;
    }

    .controls-section, .section {
        padding-left: 2em;
    }
     a {
        color: var(--picton-blue);
     }
     .how-to-play {
        background-color: var(--rich-black);
        color: var(--white);
        overflow-wrap: anywhere;
        height: 100%;
        margin: 0px;
        padding: 1em;
        padding-right: 2em;
        width: 100%;
        box-sizing: border-box;
    }
</style>