<template>
  <div class="flex flex-col items-center">
    <div style="position: relative; width: 390px; height: 700px;">
      <canvas ref="canvas" width="390" height="700" style="border:1px solid #000; background-color: #65acee;"></canvas>
      <button ref="restartButton" @click="restartGame" style="display: none; position: absolute; top: 60%; left: 50%; transform: translate(-50%, -50%);">Restart</button>
      <!-- <input type="text" ref="nameInput" placeholder="Enter your name" style="position: absolute; bottom: 40px; left: 50%; transform: translateX(-50%);" /> -->
      <!-- <button @click="submitScore" style="position: absolute; bottom: 10px; left: 50%; transform: translateX(-50%);">Submit Score</button> -->
      <div ref="highscoreList" style="position: absolute; top: 10px; left: 50%; transform: translateX(-50%); width: 100%; text-align: center;"></div>
    </div>
    <div style="display: flex; justify-content: center; margin-top: 10px;">
      <button @pointerdown="moveLeft" @pointerup="stopMove" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-4 px-6 rounded-lg">Left</button>
      <button @pointerdown="moveRight" @pointerup="stopMove" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-4 px-6 rounded-lg ml-4">Right</button>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';

const canvas = ref(null);
const restartButton = ref(null);
const nameInput = ref(null);
const highscoreList = ref(null);
let player, pil, pil1, pil2, pil3, pil4, pil5, pil6, pil7, pil8, pil9, pil10, pil11, pil12, pil13, pil14, pil15, coin, coin1, coin2, coin3, coin4, pille, pille1, pille2, myObstacle, vegg, vegg1, vegg2;
let pills = false, pillact = false, coinsz = false, newskins = false, burger = false;
let score = 0;

const myGameArea = {
  canvas: null,
  context: null,
  interval: null,
  start() {
    this.canvas = canvas.value;
    this.context = this.canvas.getContext("2d");
    this.interval = setInterval(updateGameArea, 20);
  },
  clear() {
    this.context.clearRect(0, 0, this.canvas.width, this.canvas.height);
  },
  stop() {
    clearInterval(this.interval);
  },
};

function component(width, height, color, x, y, type) {
  this.type = type;
  if (type === "image") {
    this.image = new Image();
    this.image.src = color;
  }
  this.width = width;
  this.height = height;
  this.speedX = 0;
  this.speedY = 0;
  this.x = x;
  this.y = y;
  this.update = function () {
    const ctx = myGameArea.context;
    if (type === "image") {
      ctx.drawImage(this.image, this.x, this.y, this.width, this.height);
    } else {
      ctx.fillStyle = color;
      ctx.fillRect(this.x, this.y, this.width, this.height);
    }
  };
  this.newPos = function () {
    this.x += this.speedX;
    this.y += this.speedY;
  };
  this.crashWith = function (otherobj) {
    const myleft = this.x;
    const myright = this.x + this.width;
    const mytop = this.y;
    const mybottom = this.y + this.height;
    const otherleft = otherobj.x;
    const otherright = otherobj.x + otherobj.width;
    const othertop = otherobj.y;
    const otherbottom = otherobj.y + otherobj.height;
    let crash = true;
    if (
      mybottom < othertop ||
      mytop > otherbottom ||
      myright < otherleft ||
      myleft > otherright
    ) {
      crash = false;
    }
    return crash;
  };
}

function updateGameArea() {
  if (
    player.crashWith(pil) ||
    player.crashWith(pil1) ||
    player.crashWith(pil2) ||
    player.crashWith(pil3) ||
    player.crashWith(pil4) ||
    player.crashWith(pil5) ||
    player.crashWith(pil6) ||
    player.crashWith(pil7) ||
    player.crashWith(pil8) ||
    player.crashWith(pil9) ||
    player.crashWith(pil10) ||
    player.crashWith(pil11) ||
    player.crashWith(pil12) ||
    player.crashWith(pil13) ||
    player.crashWith(pil14) ||
    player.crashWith(pil15)
  ) {
    const ctx = myGameArea.context;
    ctx.font = "25px Sans-serif";
    ctx.fillStyle = "black";
    ctx.fillText("Game Over", 135, 350);

    myGameArea.stop();
    restartButton.value.style.display = "block";
  } else {
    myGameArea.clear();
    vegg.update();
    vegg1.update();
    vegg2.update();
    player.newPos();
    player.update();
    walls();
    pill();
    pil.y += 8;
    pil.update();
    drawScore();
    coinz();
    invincible();
    myObstacle.update();

    if (coinsz === true) {
      coinvalue();
    }
    if (pills === true) {
      inv();
    }
    if (pillact === true) {
      pillactive();
    }
  }
  if (newskins === true && burger === false && score === 0) {
    const ctx = myGameArea.context;
    ctx.font = "15px Sans-serif";
    ctx.fillStyle = "black";
    ctx.fillText("Playing as: Birgit", 27, 43);
  }
  if (newskins === false && burger === false && score === 0) {
    const ctx = myGameArea.context;
    ctx.font = "15px Sans-serif";
    ctx.fillStyle = "black";
    ctx.fillText("Playing as: OG Birger", 27, 43);
  }
  if (newskins === false && burger === true && score === 0) {
    const ctx = myGameArea.context;
    ctx.font = "15px Sans-serif";
    ctx.fillStyle = "black";
    ctx.fillText("Playing as: Burger", 27, 43);
  }
}

function walls() {
  if (player.x <= 15) {
    player.x = 15;
  }
  if (player.x >= 340) {
    player.x = 340;
  }
}

function pill() {
  if (pil.y >= 710) {
    pil.y = Math.floor(Math.random() * 200);
    pil.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (score >= 5) {
    pil1.y += 10;
    pil1.update();
    if (pil1.y >= 710) {
      pil1.y = Math.floor(Math.random() * 250);
      pil1.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 15) {
    pil2.y += 11;
    pil2.update();
    if (pil2.y >= 710) {
      pil2.y = Math.floor(Math.random() * 250);
      pil2.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 30) {
    pil3.y += 12;
    pil3.update();
    if (pil3.y >= 710) {
      pil3.y = Math.floor(Math.random() * 300);
      pil3.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 50) {
    pil4.y += 13;
    pil4.update();
    if (pil4.y >= 710) {
      pil4.y = Math.floor(Math.random() * 400);
      pil4.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 75) {
    pil5.y += 14;
    pil5.update();
    if (pil5.y >= 710) {
      pil5.y = Math.floor(Math.random() * 400);
      pil5.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 100) {
    pil6.y += 15;
    pil6.update();
    if (pil6.y >= 710) {
      pil6.y = Math.floor(Math.random() * 400);
      pil6.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 130) {
    pil7.y += 16;
    pil7.update();
    if (pil7.y >= 710) {
      pil7.y = Math.floor(Math.random() * 400);
      pil7.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 170) {
    pil8.y += 17;
    pil8.update();
    if (pil8.y >= 710) {
      pil8.y = Math.floor(Math.random() * 400);
      pil8.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 200) {
    pil9.y += 18;
    pil9.update();
    if (pil9.y >= 710) {
      pil9.y = Math.floor(Math.random() * 500);
      pil9.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 250) {
    pil10.y += 19;
    pil10.update();
    if (pil10.y >= 710) {
      pil10.y = Math.floor(Math.random() * 500);
      pil10.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 275) {
    pil11.y += 20;
    pil11.update();
    if (pil11.y >= 710) {
      pil11.y = Math.floor(Math.random() * 500);
      pil11.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 300) {
    pil12.y += 21;
    pil12.update();
    if (pil12.y >= 710) {
      pil12.y = Math.floor(Math.random() * 600);
      pil12.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (score >= 350) {
    pil13.y += 22;
    pil13.update();
    pil14.y += 23;
    pil14.update();
    pil15.y += 24;
    pil15.update();

    if (pil13.y >= 710) {
      pil13.y = Math.floor(Math.random() * 300);
      pil13.x = Math.floor(Math.random() * 350);
      score++;
    }
  }
  if (pil14.y >= 710) {
    pil14.y = Math.floor(Math.random() * 300);
    pil14.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil15.y >= 710) {
    pil15.y = Math.floor(Math.random() * 300);
    pil15.x = Math.floor(Math.random() * 350);
    score++;
  }
}

function coinz() {
  if (score > 3) {
    coin.y += 9;
    coin.update();
  }
  if (player.crashWith(coin)) {
    coin.y += 10000;
    coinsz = true;
  }
  if (score > 13) {
    coinsz = false;
  }

  if (score > 87) {
    coin1.y += 7;
    coin1.update();
  }
  if (player.crashWith(coin1)) {
    coin1.y += 10000;
    coinsz = true;
  }
  if (score > 97) {
    coinsz = false;
  }

  if (score > 164) {
    coin2.y += 9;
    coin2.update();
  }
  if (player.crashWith(coin2)) {
    coin2.y += 10000;
    coinsz = true;
  }
  if (score > 174) {
    coinsz = false;
  }

  if (score > 256) {
    coin3.y += 9;
    coin3.update();
  }
  if (player.crashWith(coin3)) {
    coin3.y += 10000;
    coinsz = true;
  }
  if (score > 266) {
    coinsz = false;
  }

  if (score > 340) {
    coin4.y += 9;
    coin4.update();
  }
  if (player.crashWith(coin4)) {
    coin4.y += 10000;
    coinsz = true;
  }
  if (score > 350) {
    coinsz = false;
  }
}

function coinvalue() {
  score += 10;
}

function invincible() {
  if (score > 16) {
    pille.y += 9;
    pille.update();
  }
  if (player.crashWith(pille)) {
    pille.y += 10000;
    pills = true;
    pillact = true;
  }

  if (score === 35) {
    pills = false;
    pillact = false;
  }
  if (score > 127) {
    pille1.y += 7;
    pille1.update();
  }
  if (player.crashWith(pille1)) {
    pille1.y += 10000;
    pills = true;
    pillact = true;
  }
  if (score === 179) {
    pills = false;
    pillact = false;
  }
  if (score > 302) {
    pille2.y += 7;
    pille2.update();
  }
  if (player.crashWith(pille2)) {
    pille2.y += 10000;
    pills = true;
    pillact = true;
  }
  if (score > 357) {
    pills = false;
    pillact = false;
  }
}

function inv() {
  if (pil.y >= 649) {
    pil.y = Math.floor(Math.random() * 200);
    pil.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil1.y >= 649) {
    pil1.y = Math.floor(Math.random() * 200);
    pil1.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil2.y >= 649) {
    pil2.y = Math.floor(Math.random() * 200);
    pil2.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil3.y >= 649) {
    pil3.y = Math.floor(Math.random() * 200);
    pil3.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil4.y >= 649) {
    pil4.y = Math.floor(Math.random() * 200);
    pil4.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil5.y >= 649) {
    pil5.y = Math.floor(Math.random() * 200);
    pil5.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil6.y >= 649) {
    pil6.y = Math.floor(Math.random() * 200);
    pil6.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil7.y >= 649) {
    pil7.y = Math.floor(Math.random() * 200);
    pil7.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil8.y >= 649) {
    pil8.y = Math.floor(Math.random() * 200);
    pil8.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil9.y >= 649) {
    pil9.y = Math.floor(Math.random() * 200);
    pil9.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil10.y >= 649) {
    pil10.y = Math.floor(Math.random() * 200);
    pil10.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil11.y >= 649) {
    pil11.y = Math.floor(Math.random() * 200);
    pil11.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil12.y >= 649) {
    pil12.y = Math.floor(Math.random() * 200);
    pil12.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil13.y >= 649) {
    pil13.y = Math.floor(Math.random() * 200);
    pil13.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil14.y >= 649) {
    pil14.y = Math.floor(Math.random() * 200);
    pil14.x = Math.floor(Math.random() * 350);
    score++;
  }
  if (pil15.y >= 649) {
    pil15.y = Math.floor(Math.random() * 200);
    pil15.x = Math.floor(Math.random() * 350);
    score++;
  }
}

function drawScore() {
  const ctx = myGameArea.context;
  ctx.font = "20px Arial";
  ctx.fillStyle = "black";
  ctx.fillText(`Score: ${score}`, 280, 50);
}

function pillactive() {
  const ctx = myGameArea.context;
  ctx.font = "20px Arial";
  ctx.fillStyle = "black";
  ctx.fillText("Pill Active", 20, 50);
}

function submitScore() {
  const name = nameInput.value;
  if (!name) return alert("No name");
  fetch("https://api.birgerrun.ikomm.ollebolle.xyz/highscore", {
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ name, score }),
    method: "POST",
  }).then(function () {
    window.location.reload();
  });
}

function restartGame() {
  restartButton.value.style.display = "none";
  myGameArea.clear();
  score = 0;
  startGame();
}

function startGame() {
  player = new component(35, 50, "/mid.png", 175, 600, "image");
  pil = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil1 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil2 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil3 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil4 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil5 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil6 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil7 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil8 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil9 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil10 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil11 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil12 = new component(20, 37, "/bgpoison.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil13 = new component(25, 45, "/sjoko.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil14 = new component(25, 45, "/sjoko.jpg", Math.floor(Math.random() * 350), 100, "image");
  pil15 = new component(25, 45, "/sjoko.jpg", Math.floor(Math.random() * 350), 100, "image");
  coin = new component(30, 30, "/coins.png", Math.floor(Math.random() * 350), 100, "image");
  coin1 = new component(30, 30, "/coins.png", Math.floor(Math.random() * 350), 100, "image");
  coin2 = new component(30, 30, "/coins.png", Math.floor(Math.random() * 350), 100, "image");
  coin3 = new component(30, 30, "/coins.png", Math.floor(Math.random() * 350), 100, "image");
  coin4 = new component(30, 30, "/coins.png", Math.floor(Math.random() * 350), 100, "image");
  pille = new component(30, 30, "/pille.png", Math.floor(Math.random() * 350), 100, "image");
  pille1 = new component(30, 30, "/pille.png", Math.floor(Math.random() * 350), 100, "image");
  pille2 = new component(30, 30, "/pille.png", Math.floor(Math.random() * 350), 100, "image");

  myObstacle = new component(390, 50, "#1e971a", 0, 650);
  vegg = new component(390, 25, "#3f6fb8", 0, 0);
  vegg1 = new component(25, 650, "#3f6fb8", 0, 0);
  vegg2 = new component(25, 650, "#3f6fb8", 365, 0);

  if (newskins === true && burger === false) {
    player = new component(35, 50, "/mid1.png", 175, 600, "image");

    if (newskins === false && burger === true) {
      player = new component(35, 50, "/mid2.png", 175, 600, "image");
    }
    if (newskins === false && burger === false)
      player = new component(35, 50, "/mid.png", 175, 600, "image");
  }

  myGameArea.start();
}

function moveLeft() {
  player.speedX = -6;
  if (newskins == false && burger == true) {
    player.image.src = "/left2.png";
  } else if (newskins == true && burger == false) {
    player.image.src = "/left1.png";
  } else {
    player.image.src = "/left.png";
  }
}

function moveRight() {
  player.speedX = 6;
  if (newskins == false && burger == true) {
    player.image.src = "/right2.png";
  } else if (newskins == true && burger == false) {
    player.image.src = "/right1.png";
  } else {
    player.image.src = "/right.png";
  }
}

function stopMove() {
  if (newskins == false && burger == true) {
    player.image.src = "/mid2.png";
  } else if (newskins == true && burger == false) {
    player.image.src = "/mid1.png";
  } else {
    player.image.src = "/mid.png";
  }
  player.speedX = 0;
}




onMounted(() => {
  restartButton.value.style.display = "none";
  fetch("https://api.birgerrun.ikomm.ollebolle.xyz/highscore").then((res) => {
    res.json().then((data) => {
      let count = 1;
      data.forEach((obj) => {
        const element = document.createElement("p");
        element.className = "fetched-highscore";
        element.innerHTML = `${count}. <b>${obj.name}</b> - ${obj.score}`;
        highscoreList.value.appendChild(element);
        count++;
      });
    });
  });
  startGame();

  document.onkeydown = function (evt) {
    const tallkode = evt.keyCode;
    if (tallkode === 37 || tallkode === 65) {
      player.speedX = -6;
      if (newskins == false && burger == true) {
        player.image.src = "/left2.png";
      } else if (newskins == true && burger == false) {
        player.image.src = "/left1.png";
      } else {
        player.image.src = "/left.png";
      }
    }
    if (tallkode === 39 || tallkode === 68) {
      player.speedX = 6;
      if (newskins == false && burger == true) {
        player.image.src = "/right2.png";
      } else if (newskins == true && burger == false) {
        player.image.src = "/right1.png";
      } else {
        player.image.src = "/right.png";
      }
    }
  };

  document.onkeyup = function (evt) {
    if (newskins == false && burger == true) {
      player.image.src = "/mid2.png";
    } else if (newskins == true && burger == false) {
      player.image.src = "/mid1.png";
    } else {
      player.image.src = "/mid.png";
    }

    const tallkode = evt.keyCode;
    if (tallkode === 37 && player.speedX === -6) {
      player.speedX = 0;
    }
    if (tallkode === 39 && player.speedX === 6) {
      player.speedX = 0;
    }
    if (tallkode === 65 && player.speedX === -6) {
      player.speedX = 0;
    }
    if (tallkode === 68 && player.speedX === 6) {
      player.speedX = 0;
    }
  };
});
</script>

<style scoped>
/* Add any styles for your canvas here */
</style>
