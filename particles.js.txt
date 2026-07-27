/* ===================================
   TETRIS NOVA
   Космические частицы ✨
=================================== */


const particleCanvas = document.createElement("canvas");

particleCanvas.id = "particleLayer";

document.body.appendChild(particleCanvas);


const particleCtx =
particleCanvas.getContext("2d");


let particles = [];



function resizeParticles(){

    particleCanvas.width =
    window.innerWidth;

    particleCanvas.height =
    window.innerHeight;

}


resizeParticles();


window.addEventListener(
"resize",
resizeParticles
);




class Particle {


    constructor(x,y,color){


        this.x=x;

        this.y=y;

        this.size=
        Math.random()*5+2;


        this.speedX=
        (Math.random()-0.5)*8;


        this.speedY=
        (Math.random()-0.5)*8;


        this.life=1;


        this.color=color;


    }




    update(){


        this.x+=this.speedX;

        this.y+=this.speedY;


        this.life-=0.02;


        this.size*=0.97;


    }




    draw(){


        particleCtx.save();


        particleCtx.globalAlpha=
        this.life;


        particleCtx.fillStyle=
        this.color;


        particleCtx.shadowBlur=15;

        particleCtx.shadowColor=
        this.color;



        particleCtx.beginPath();


        particleCtx.arc(

            this.x,

            this.y,

            this.size,

            0,

            Math.PI*2

        );


        particleCtx.fill();


        particleCtx.restore();


    }



}




function createExplosion(x,y,color){


    for(
        let i=0;
        i<35;
        i++
    ){


        particles.push(

            new Particle(
                x,
                y,
                color
            )

        );


    }


}





function animateParticles(){


    particleCtx.clearRect(

        0,
        0,
        particleCanvas.width,
        particleCanvas.height

    );



    particles.forEach(
        (p,index)=>{


            p.update();

            p.draw();



            if(p.life<=0){

                particles.splice(
                    index,
                    1
                );

            }


        }

    );



    requestAnimationFrame(
        animateParticles
    );


}


animateParticles();