/* ===================================
   TETRIS NOVA
   Космический звук 🎵
=================================== */


let audioEnabled = true;


/*
    Создаем звуки через Web Audio API
    Не нужны отдельные mp3-файлы
*/


const AudioContext =
window.AudioContext ||
window.webkitAudioContext;


let audioContext;



function initAudio(){

    if(!audioContext){

        audioContext =
        new AudioContext();

    }

}




function playTone(
    frequency,
    duration,
    type="sine"
){


    if(!audioEnabled)
        return;


    initAudio();



    const oscillator =
    audioContext.createOscillator();


    const gain =
    audioContext.createGain();



    oscillator.type =
    type;


    oscillator.frequency.value =
    frequency;



    gain.gain.value =
    0.08;



    oscillator.connect(gain);

    gain.connect(
        audioContext.destination
    );



    oscillator.start();


    gain.gain.exponentialRampToValueAtTime(

        0.001,

        audioContext.currentTime
        + duration

    );


    oscillator.stop(

        audioContext.currentTime
        + duration

    );

}




// движение фигуры

function soundMove(){

    playTone(
        180,
        0.05,
        "square"
    );

}



// поворот

function soundRotate(){

    playTone(
        320,
        0.08,
        "triangle"
    );

}



// падение

function soundDrop(){

    playTone(
        90,
        0.12,
        "square"
    );

}



// очистка линии

function soundClear(){

    playTone(
        600,
        0.2,
        "sine"
    );

}



// новый уровень

function soundLevel(){

    playTone(
        900,
        0.3,
        "triangle"
    );

}



// режим кометы ☄️

function soundComet(){

    playTone(
        1200,
        0.5,
        "sawtooth"
    );

}