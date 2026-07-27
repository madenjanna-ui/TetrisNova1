/* ===================================
   TETRIS NOVA
   ⭐ Звёзды тебя ждут...
=================================== */


const canvas = document.getElementById("gameBoard");
const ctx = canvas.getContext("2d");

const nextCanvas = document.getElementById("nextPiece");
const nextCtx = nextCanvas.getContext("2d");


const startButton = document.getElementById("startButton");
const restartButton = document.getElementById("restartButton");

const menu = document.getElementById("menu");
const gameScreen = document.getElementById("gameScreen");
const gameOverScreen = document.getElementById("gameOver");


const scoreText = document.getElementById("score");
const levelText = document.getElementById("level");
const linesText = document.getElementById("lines");
const highScoreText = document.getElementById("highScore");

const finalScore = document.getElementById("finalScore");



/*
    НАСТРОЙКИ ПОЛЯ
*/


const ROWS = 20;
const COLS = 10;

const BLOCK = 30;


let board;

let current;

let next;

let score = 0;
let lines = 0;
let level = 1;

let gameInterval;

let running = false;



/*
    ФИГУРЫ
*/


const pieces = [

    {
        shape:[
            [1,1,1,1]
        ],
        color:"#00eaff"
    },


    {
        shape:[
            [1,1],
            [1,1]
        ],
        color:"#ffd900"
    },


    {
        shape:[
            [0,1,0],
            [1,1,1]
        ],
        color:"#b05cff"
    },


    {
        shape:[
            [1,1,0],
            [0,1,1]
        ],
        color:"#36ff6a"
    },


    {
        shape:[
            [0,1,1],
            [1,1,0]
        ],
        color:"#ff4f4f"
    },


    {
        shape:[
            [1,0,0],
            [1,1,1]
        ],
        color:"#ff8c00"
    },


    {
        shape:[
            [0,0,1],
            [1,1,1]
        ],
        color:"#4287ff"
    }


];




function createBoard(){

    board=[];

    for(let r=0;r<ROWS;r++){

        board.push(
            new Array(COLS).fill(0)
        );

    }

}





function randomPiece(){

    const p =
    pieces[
        Math.floor(Math.random()*pieces.length)
    ];


    return {

        shape:p.shape.map(row=>[...row]),

        color:p.color,

        x:3,

        y:0

    };

}





/*
    РИСОВАНИЕ
*/


function draw(){

    ctx.clearRect(
        0,
        0,
        canvas.width,
        canvas.height
    );


    for(let r=0;r<ROWS;r++){

        for(let c=0;c<COLS;c++){

            if(board[r][c]){

                drawBlock(
                    c,
                    r,
                    board[r][c]
                );

            }

        }

    }



    if(current){

        current.shape.forEach((row,y)=>{

            row.forEach((value,x)=>{


                if(value){

                    drawBlock(

                        current.x+x,

                        current.y+y,

                        current.color

                    );

                }

            });

        });

    }

}





function drawBlock(x,y,color){


    ctx.fillStyle=color;


    ctx.shadowBlur=15;

    ctx.shadowColor=color;


    ctx.fillRect(

        x*BLOCK,

        y*BLOCK,

        BLOCK-2,

        BLOCK-2

    );


    ctx.shadowBlur=0;


}





/*
    ДВИЖЕНИЕ
*/


function collision(){

    for(let y=0;y<current.shape.length;y++){

        for(let x=0;x<current.shape[y].length;x++){


            if(
                current.shape[y][x] &&

                (
                board[current.y+y] &&
                board[current.y+y][current.x+x]
                ||

                current.x+x<0 ||

                current.x+x>=COLS ||

                current.y+y>=ROWS
                )

            ){

                return true;

            }

        }

    }

    return false;

}




function moveDown(){


    current.y++;


    if(collision()){


        current.y--;


        merge();


        clearLines();


        current=next;

        next=randomPiece();


        if(collision()){

            endGame();

        }

    }


}




function move(dir){


    current.x+=dir;


    if(collision()){

        current.x-=dir;

    }

}





function rotate(){


    let old=current.shape;


    current.shape =
    current.shape[0].map(
        (_,i)=>
        current.shape.map(
            row=>row[i]
        ).reverse()
    );



    if(collision()){

        current.shape=old;

    }


}




function merge(){


    current.shape.forEach((row,y)=>{

        row.forEach((value,x)=>{


            if(value){

                board[
                    current.y+y
                ][
                    current.x+x
                ]
                =
                current.color;

            }


        });


    });


}





function clearLines(){


    let count=0;


    for(let y=ROWS-1;y>=0;y--){


        if(board[y].every(Boolean)){

createExplosion(
    canvas.width / 2,
    y * BLOCK,
    "#65dcff"
);


            board.splice(y,1);


            board.unshift(
                new Array(COLS).fill(0)
            );


            count++;


            y++;

        }


    }



    if(count){


        lines+=count;


        score+=count*100;


        level=
        Math.floor(lines/10)+1;


        updateStats();

    }


}




function updateStats(){


    scoreText.textContent=score;

    linesText.textContent=lines;

    levelText.textContent=level;


    let record =
    localStorage.getItem(
        "tetrisNovaRecord"
    ) || 0;


    if(score>record){

        localStorage.setItem(
            "tetrisNovaRecord",
            score
        );

        record=score;

    }


    highScoreText.textContent=record;

}





/*
    ИГРА
*/


function startGame(){


    menu.classList.add("hidden");

    gameScreen.classList.remove("hidden");


    createBoard();


    current=randomPiece();

    next=randomPiece();


    score=0;

    lines=0;

    level=1;


    updateStats();


    running=true;


    gameInterval=setInterval(
        moveDown,
        700
    );


    drawLoop();

}





function drawLoop(){

    if(running){

        draw();

        requestAnimationFrame(
            drawLoop
        );

    }

}




function endGame(){


    running=false;


    clearInterval(
        gameInterval
    );


    gameScreen.classList.add("hidden");


    gameOverScreen.classList.remove("hidden");


    finalScore.textContent=score;


}





startButton.onclick=startGame;


restartButton.onclick=()=>{


    gameOverScreen.classList.add("hidden");

    startGame();

};





document.addEventListener(
"keydown",
e=>{


    if(!running)
        return;


    if(e.key==="ArrowLeft")
        move(-1);


    if(e.key==="ArrowRight")
        move(1);


    if(e.key==="ArrowDown")
        moveDown();


    if(e.key==="ArrowUp")
        rotate();


});
// ============================
// МОБИЛЬНОЕ УПРАВЛЕНИЕ
// ============================


document.getElementById("left").onclick = () => {

    if(running){

        move(-1);

    }

};



document.getElementById("right").onclick = () => {

    if(running){

        move(1);

    }

};



document.getElementById("down").onclick = () => {

    if(running){

        moveDown();

    }

};



document.getElementById("rotate").onclick = () => {

    if(running){

        rotate();

    }

};