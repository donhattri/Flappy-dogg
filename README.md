# Flappy-dogg
<!DOCTYPE html>
<html>
<head>
  <title>Flappy Bird Clone</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.2/p5.min.js"></script>
</head>
<body>
<script>
let bird;
let pipes = [];
let score = 0;
let gameOver = false;

function setup() {
  createCanvas(400, 600);
  bird = new Bird();
  pipes.push(new Pipe());
}

function draw() {
  background(0);
  
  if (!gameOver) {
    // Update and show bird
    bird.update();
    bird.show();
    
    // Update and show pipes
    for (let i = pipes.length - 1; i >= 0; i--) {
      pipes[i].show();
      pipes[i].update();
      
      // Check collision
      if (pipes[i].hits(bird)) {
        gameOver = true;
      }
      
      // Increase score when passing pipe
      if (pipes[i].passes(bird)) {
        score += 1;
      }
      
      // Remove offscreen pipes
      if (pipes[i].offscreen()) {
        pipes.splice(i, 1);
      }
    }
    
    // Add new pipe every 100 frames
    if (frameCount % 100 == 0) {
      pipes.push(new Pipe());
    }
    
    // Display score
    fill(255);
    textSize(32);
    text(score, width / 2, 50);
  } else {
    // Game over screen
    fill(255);
    textSize(32);
    textAlign(CENTER);
    text("Game Over!", width / 2, height / 2);
    text("Score: " + score, width / 2, height / 2 + 40);
    text("Press R to Restart", width / 2, height / 2 + 80);
  }
}

function keyPressed() {
  if (key == ' ' && !gameOver) {
    bird.up();
  }
  if (key == 'r' && gameOver) {
    resetGame();
  }
}

function Bird() {
  this.y = height / 2;
  this.x = 64;
  this.gravity = 0.6;
  this.lift = -15;
  this.velocity = 0;
  
  this.show = function() {
    fill(255, 255, 0);
    ellipse(this.x, this.y, 32, 32);
  }
  
  this.up = function() {
    this.velocity += this.lift;
  }
  
  this.update = function() {
    this.velocity += this.gravity;
    this.velocity *= 0.9; // Air resistance
    this.y += this.velocity;
    
    // Prevent bird from going off screen
    if (this.y > height) {
      this.y = height;
      this.velocity = 0;
    }
    if (this.y < 0) {
      this.y = 0;
      this.velocity = 0;
    }
  }
}

function Pipe() {
  this.spacing = 175;
  this.top = random(height / 6, 3 / 4 * height);
  this.bottom = height - (this.top + this.spacing);
  this.x = width;
  this.w = 80;
  this.speed = 2;
  this.passed = false;
  
  this.show = function() {
    fill(0, 255, 0);
    rect(this.x, 0, this.w, this.top);
    rect(this.x, height - this.bottom, this.w, this.bottom);
  }
  
  this.update = function() {
    this.x -= this.speed;
  }
  
  this.offscreen = function() {
    return this.x < -this.w;
  }
  
  this.hits = function(bird) {
    if (bird.y < this.top || bird.y > height - this.bottom) {
      if (bird.x > this.x && bird.x < this.x + this.w) {
        return true;
      }
    }
    return false;
  }
  
  this.passes = function(bird) {
    if (!this.passed && bird.x > this.x + this.w) {
      this.passed = true;
      return true;
    }
    return false;
  }
}

function resetGame() {
  bird = new Bird();
  pipes = [];
  pipes.push(new Pipe());
  score = 0;
  gameOver = false;
  loop();
}
</script>
</body>
</html>
