<script>
    import { onMount } from "svelte";
    import { writable } from "svelte/store";
    let intervals = {}; 
    let leftPos = $state(50);
    let rightPos = $state(50);
    let bottomPos = $state(50);
    let topPos = $state(50);
    let ballAngle = 0;
    const motionInterval = 1000/ 60 //Denomintator is FPS.
    let ballSpeed = 0.5;
    let paddleSpeed = ballSpeed*1.5;
    let ballX = $state(50);
    let ballY = $state(50);
    let container, ball, lPaddle, rPaddle, tPaddle, bPaddle;
    let lastHit = 0;
    let score = $state(0);
    let DataElement;
    let highScore = $state(0);
    let gameRunning = $state(false);
    let hitSound;
    let paddles = $derived([lPaddle, rPaddle, tPaddle, bPaddle]);
    // localStorage.highScore = 4;
    let accelerationStr = $state("0.01");
    let firstGame = $state(true);
    let acceleration = $derived(Number(accelerationStr));
    let bgMusic;

    function playHit() {
            hitSound.play();
    }
    function startBackgroundMusic() {
        bgMusic.loop = true;
        bgMusic.volume = 0.3; // Adjust volume as needed
        bgMusic.play();
    }

    function detectAnyOveralap(e1, eList) {
        for (let i = 0; i < eList.length; i++) {
            if (detectOverlap(e1, eList[i])) {
                console.log(paddles, eList[i])
                if (paddles.includes(eList[i])) {
                    console.log("Raa")
                    if (lastHit == eList[i]) {
                        console.log(lastHit)
                        // lastHit = eList[i];
                        return false;
                    }
                    lastHit = eList[i];
                }
                return true;
            }
        }
        return false;
    }
    var detectOverlap = (function () {
        function getPositions(elem) {
            var pos = elem.getBoundingClientRect();
            return [[pos.left, pos.right], [pos.top, pos.bottom]];
        }

        function comparePositions(p1, p2) {
            var r1, r2;
            if (p1[0] < p2[0]) {
            r1 = p1;
            r2 = p2;
            } else {
            r1 = p2;
            r2 = p1;
            }
            return r1[1] > r2[0] || r1[0] === r2[0];
        }

        return function (a, b) {
            if ((a == undefined) || (b == undefined)) {
                return false;
            }
            var pos1 = getPositions(a),
                pos2 = getPositions(b);
            return comparePositions(pos1[0], pos2[0]) && comparePositions(pos1[1], pos2[1]);
        };
    })();
    function  startGame() {
        
        ballX = 50;
        ballY = 50;
        ballAngle = Math.random()*360;
        ballSpeed = 0.5;
        score = 0;
        leftPos = 50;
        rightPos = 50;
        bottomPos = 50;
        topPos = 50;
        if (window.mobileCheck()) {
            alert("Sorry but this game does not work on mobile, please skip voting or switch to desktop");
            gameRunning = false;
            return;
        }
        startBackgroundMusic();
        firstGame = false;
    }

    onMount(() => {
        if (window.location.href.includes("firstLoad")) {
            window.location.href = window.location.href.replace("firstLoad", "");
            // location.reload();
        }
        // alert("Ae you mobile: "+isMobile);
        window.mobileCheck = function() {
        let check = false;
        (function(a){if(/(android|bb\d+|meego).+mobile|avantgo|bada\/|blackberry|blazer|compal|elaine|fennec|hiptop|iemobile|ip(hone|od)|iris|kindle|lge |maemo|midp|mmp|mobile.+firefox|netfront|opera m(ob|in)i|palm( os)?|phone|p(ixi|re)\/|plucker|pocket|psp|series(4|6)0|symbian|treo|up\.(browser|link)|vodafone|wap|windows ce|xda|xiino/i.test(a)||/1207|6310|6590|3gso|4thp|50[1-6]i|770s|802s|a wa|abac|ac(er|oo|s\-)|ai(ko|rn)|al(av|ca|co)|amoi|an(ex|ny|yw)|aptu|ar(ch|go)|as(te|us)|attw|au(di|\-m|r |s )|avan|be(ck|ll|nq)|bi(lb|rd)|bl(ac|az)|br(e|v)w|bumb|bw\-(n|u)|c55\/|capi|ccwa|cdm\-|cell|chtm|cldc|cmd\-|co(mp|nd)|craw|da(it|ll|ng)|dbte|dc\-s|devi|dica|dmob|do(c|p)o|ds(12|\-d)|el(49|ai)|em(l2|ul)|er(ic|k0)|esl8|ez([4-7]0|os|wa|ze)|fetc|fly(\-|_)|g1 u|g560|gene|gf\-5|g\-mo|go(\.w|od)|gr(ad|un)|haie|hcit|hd\-(m|p|t)|hei\-|hi(pt|ta)|hp( i|ip)|hs\-c|ht(c(\-| |_|a|g|p|s|t)|tp)|hu(aw|tc)|i\-(20|go|ma)|i230|iac( |\-|\/)|ibro|idea|ig01|ikom|im1k|inno|ipaq|iris|ja(t|v)a|jbro|jemu|jigs|kddi|keji|kgt( |\/)|klon|kpt |kwc\-|kyo(c|k)|le(no|xi)|lg( g|\/(k|l|u)|50|54|\-[a-w])|libw|lynx|m1\-w|m3ga|m50\/|ma(te|ui|xo)|mc(01|21|ca)|m\-cr|me(rc|ri)|mi(o8|oa|ts)|mmef|mo(01|02|bi|de|do|t(\-| |o|v)|zz)|mt(50|p1|v )|mwbp|mywa|n10[0-2]|n20[2-3]|n30(0|2)|n50(0|2|5)|n7(0(0|1)|10)|ne((c|m)\-|on|tf|wf|wg|wt)|nok(6|i)|nzph|o2im|op(ti|wv)|oran|owg1|p800|pan(a|d|t)|pdxg|pg(13|\-([1-8]|c))|phil|pire|pl(ay|uc)|pn\-2|po(ck|rt|se)|prox|psio|pt\-g|qa\-a|qc(07|12|21|32|60|\-[2-7]|i\-)|qtek|r380|r600|raks|rim9|ro(ve|zo)|s55\/|sa(ge|ma|mm|ms|ny|va)|sc(01|h\-|oo|p\-)|sdk\/|se(c(\-|0|1)|47|mc|nd|ri)|sgh\-|shar|sie(\-|m)|sk\-0|sl(45|id)|sm(al|ar|b3|it|t5)|so(ft|ny)|sp(01|h\-|v\-|v )|sy(01|mb)|t2(18|50)|t6(00|10|18)|ta(gt|lk)|tcl\-|tdg\-|tel(i|m)|tim\-|t\-mo|to(pl|sh)|ts(70|m\-|m3|m5)|tx\-9|up(\.b|g1|si)|utst|v400|v750|veri|vi(rg|te)|vk(40|5[0-3]|\-v)|vm40|voda|vulc|vx(52|53|60|61|70|80|81|83|85|98)|w3c(\-| )|webc|whit|wi(g |nc|nw)|wmlb|wonu|x700|yas\-|your|zeto|zte\-/i.test(a.substr(0,4))) check = true;})(navigator.userAgent||navigator.vendor||window.opera);
        return check;
        };
        if (window.mobileCheck()) {
            // alert(isMobile);
            alert("Sorry but this game does not work on mobile, please skip voting or switch to desktop");
        };
        hitSound.volume = 0;
        hitSound.play();
        hitSound.volume = 1;
        // startGame();
        setInterval(() => {
            bgMusic.volume = (gameRunning ? 0.3 : 0);
            if (gameRunning) {
                highScore = localStorage.highScore ? localStorage.highScore : 0;
                if (score > highScore) {
                    localStorage.highScore = score;
                }
                ballX += Math.sin(ballAngle * Math.PI/180)*ballSpeed;
                ballY += Math.cos(ballAngle * Math.PI/180)*ballSpeed;
                

                if (!detectOverlap(ball, container)){
                    startGame();
                    gameRunning = false;
                }

                // if (!(
                //     (ball.getBoundingClientRect().left < container.getBoundingClientRect().left) ||
                //     (ball.getBoundingClientRect().right > container.getBoundingClientRect().right) ||
                //     (ball.getBoundingClientRect().top < container.getBoundingClientRect().top) ||
                //     (ball.getBoundingClientRect().bottom < container.getBoundingClientRect().bottom)
                // )) {
                //     console.log("sigma");
                //     startGame();
                //     gameRunning = false;
                // }
                if (detectAnyOveralap(ball, [lPaddle, rPaddle])) {
                    ballAngle = 360 - ballAngle;
                    ballAngle += 60*(-1 + Math.random()* 2);

                    ballSpeed += acceleration;
                    score++;
                    playHit();
                } else if (detectAnyOveralap(ball, [tPaddle, bPaddle])) {
                    ballAngle = 180 - ballAngle;
                    ballAngle += 60*(-1 + Math.random()* 2);
                    ballSpeed += acceleration;
                    score++;
                    playHit();
                }
                paddleSpeed = ballSpeed*1.5;
            }
            
        }, motionInterval);
    document.addEventListener('keydown', (event) => {
        if (!intervals[event.key]) {
            intervals[event.key] = setInterval(() => {
                switch (event.key) {
                    case "ArrowUp":
                        rightPos -= paddleSpeed;
                        break;
                    case "ArrowDown":
                        rightPos += paddleSpeed;
                        break;
                    case "ArrowLeft":
                        bottomPos -= paddleSpeed;
                        break;
                    case "ArrowRight":
                        bottomPos += paddleSpeed;
                        break;
                    case "w":
                        leftPos -= paddleSpeed;
                        break;
                    case "s":
                        leftPos += paddleSpeed;
                        break;
                    case "a":
                        topPos -= paddleSpeed;
                        break;
                    case "d":
                        topPos += paddleSpeed;
                        break;
                }
                [rightPos, bottomPos, leftPos, topPos] = [rightPos, bottomPos, leftPos, topPos].map(pos => Math.max(0, Math.min(pos, 100)));
            }, 20);
        }
    });

document.addEventListener('keyup', (event) => {
    if (intervals[event.key]) {
        clearInterval(intervals[event.key]);
        delete intervals[event.key];
    }
});
    });
    
/*
Todo:
//  Add High Score;
 Use cool fornt for text;
 add sound effects;
 add dark mode;
 
*/
</script>


<div class="hyperContainer">
    <div class="data" bind:this={DataElement}>
        <span class="score">Score: {score}</span>
        <span class="highScore">High Score: {highScore}</span>
        <div style="display: flex;">
            <span style="padding-right: 0.8em;">Difficulty: </span>
            <select name="Difficulty" id="" bind:value={accelerationStr} def>
                <option value="0.01">Easy</option>
                <option value="0.02">Mid</option>
                <option value="0.06">Difficult</option>
                <option value="0.1">L'impossible</option>
                <option value="0.2">Actual Psycopath</option>
            </select>
        </div>
        <!-- <span>Speed: {acceleration}</span> -->
    </div>
    <div class="superContainer">
        <div class="container" bind:this={container}>
            {#if (!gameRunning)}
                <div class="textContainer">
                        
                    <h1>HyperPong</h1>
                    <a href="/how-to-play#firstLoad">How to play?</a>
                     <!-- <h3>skibiid</h3> -->
                    <button onclick={() => {
                        gameRunning = true;
                        startGame();
                        hitSound.play();
                    }}>{firstGame ? "Start Game" : "Play Again"}</button>
                </div>
            {/if}
                <div class="paddle left" style="--leftPos: {leftPos};--paddleWidth: {8-(1.2*Math.log(acceleration))};" bind:this={lPaddle}></div>
            <div class="paddle top" style="--topPos: {topPos};  --paddleWidth: {8-(1.2*Math.log(acceleration))};" bind:this={tPaddle}></div>
            <div class="paddle bottom" style="--bottomPos: {bottomPos};  --paddleWidth: {8-(1.2*Math.log(acceleration))};" bind:this={bPaddle}></div>
            <div class="paddle right" style="--rightPos: {rightPos};  --paddleWidth: {8-(1.2*Math.log(acceleration))};"  bind:this={rPaddle}></div>
            <div class="ball" style="
            --ballX: {ballX};
            --ballY: {ballY};
            visibility: {gameRunning ? "visible" :"hidden"};
            " bind:this={ball}></div>
        </div>
    </div>
</div>

<audio src="/hit.mp3" bind:this={hitSound}></audio>
<audio src="/music.mp3" bind:this={bgMusic}></audio>
<style>
    /* https://coolors.co/ffffff-00a7e1-00171f-003459-007ea7 */
    /* CSS HEX */
    :global(:root) {
        --white: #ffffffff;
    --picton-blue: #00a7e1ff;
    --rich-black: #00171fff;
    --prussian-blue: #003459ff;
    --cerulean: #007ea7ff;
    --unit: 1vh;
    }

    :root {
    --leftPos: 100;
    --rightPos: 100;
    --bottomPos: 100;
    --topPos: 100;
    --ballX: 50;
    --ballY: 50;
    --paddleWidth: 10;
    }

    :global(html, body) {
        margin: 0px;
        padding: 0px;
    }
    @media (orientation:portrait) {
        :root {
            --unit: 1vw;
        }
    }
    /* :global(body, html) {
        display: flex;
        align-items: center;
        justify-content: center;
        flex-direction: column;
        width: 100%;
        height: 100%;
    }
    :global(body, html) {
        background-color: var(--white);
    } */
    .hyperContainer {
        display: flex;
        align-items: center;
        justify-content: center;
        flex-direction: column;
        width: 100%;
        height: 100%;
        margin: auto;
        padding: auto;
        padding-top: 2em;
    }
    :global(*) {
        font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
        /* font-smooth: never;
        -webkit-font-smoothing : none; */
        /* font-family: ; */
        font-family: "Silkscreen", serif;
        font-weight: 400;
        font-style: normal;
    }
    .container {
        width: calc(70 * var(--unit));
        height: calc(70 * var(--unit));
        background-color: var(--rich-black);
        -webkit-box-shadow: 0px 0px 41px 14px rgba(0,0,0,0.47); 
box-shadow: 0px 0px 41px 14px rgba(0,0,0,0.47);

        display: flex;
        /* flex-direction: column; */
    }

    .paddle {
        width: calc(2 * var(--unit));
        height: calc(var(--paddleWidth) * var(--unit));
        margin: 5px;
        background-color: var(--white);
        position: fixed;
        box-shadow: 0px 0px 25px 2px rgba(167, 166, 166, 0.6);
    }

    .ball {
        width: calc(3 * var(--unit));
        height: calc(3 * var(--unit));
        background-color: var(--white);
        border-radius: 50%;
        box-shadow: 0px 0px 25px 2px rgba(167, 166, 166, 0.6);
        position: fixed;
        margin-left: calc(var(--ballX) * (67 * var(--unit))/100);
        margin-top: calc(var(--ballY) * (67 * var(--unit))/100);

    }
    .top, .bottom {
        width: calc(var(--paddleWidth) * var(--unit));
        height: calc(2 * var(--unit));
    }

    .top { 
        align-self: flex-start;
        margin-left:  max(5px, calc(var(--topPos) * ((70 - var(--paddleWidth)) * var(--unit) - 5px))/100);
    
    }

    .bottom {
        align-self: flex-end;
        
        margin-left: max(5px, calc(var(--bottomPos) * ((70 - var(--paddleWidth)) * var(--unit) - 5px))/100);
        /* margin-left:  calc((var(--bottomPos)) * calc(calc(68 * var(--unit)) - calc(9 * var(--unit)))/100 + 5px); */
    
    }

    .left {
        margin-top:  max(5px, calc(var(--leftPos) * ((70 - var(--paddleWidth)) * var(--unit) - 5px))/100);
    }

    .right {
        margin-left: calc(calc(68 * var(--unit)) - 5px);
        margin-top:  max(5px, calc(var(--rightPos) * ((70 - var(--paddleWidth)) * var(--unit) - 5px))/100);
    
    }

    .data {
        display: flex;
        flex-direction: column;
        /* justify-content: center; */
        align-items: center;
        gap: 0.2em;
        font-size: x-large;
        padding-bottom: 0.7em;
    }
    .textContainer {
        width: 100%;
        height: 100%;
        display: flex;
        z-index: 9999999999999;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        background-color: rgba(0, 0, 0, 0.2);
        & > h1 {
            color: white;
            text-shadow: 0px 0px 12px #FFFFFF;
            font-size: 3.5rem;
            margin: 0px;
            padding: 0px;
        }
        & > button {
            color: var(--cerulean);
            font-size: 1.5rem;
            background-color: var(--rich-black);
            text-shadow: 0px 0px 2px;
        }

        & > a {
            /* text-decoration: none; */
        
            text-decoration: underline;
            padding: 2em;
            
            /* color: var(--picton-blue); */
        }
        & > a:hover {
            cursor: pointer;
        }
    }

    .container {
  animation: MoveUpDown 2s linear infinite;
}

.textContainer > h1, .textContainer > button {
    animation: MoveUpDown 1s linear infinite;

}
/* 
.textContainer > button {
    
    animation: MoveLeftRight 1s linear infinite;
} */

@keyframes MoveUpDown {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-4px);
  }
}
@keyframes MoveLeftRight {
  0%, 100% {
    transform: translateX(0);
  }
  50% {
    transform: translateX(-4px);
  }
}
</style>

