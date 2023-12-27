# Vector Visualizer 

Enables te user to Add Vectors and visualize them.


![alt text](https://github.com/Joagz/matrix-visualizer/blob/master/Screenshot%202023-12-27%20162838.png)

- To open the project go to -> html -> index.html and open the file.


```javascript
const matrixSize = document.getElementById("matrixSize");
const create = document.getElementById("create");
const matrixSizeDisplay = document.getElementById("matrixSizeDisplay");

matrixSizeDisplay.innerHTML = matrixSize.value;
matrixSize.addEventListener("change", (event) => {
  matrixSizeDisplay.innerHTML = event.target.value;
});

const goCrazyGenDelay = 100;

// Once size is set create Matrix
create.addEventListener("click", () => {
  createCanvas(matrixSize.value, [], []);
});

// Create canvas, Vectors and Points
function createCanvas(gridIndexAmount, circles, vectorList) {
  matrixSize.remove();
  create.remove();

  class Vector {
    constructor(x1, y1, x2, y2, coordX, coordY) {
      this.x1 = x1;
      this.x2 = x2;
      this.y1 = y1;
      this.y2 = y2;
      this.coordX = coordX;
      this.coordY = coordY;
    }

    draw(color) {
      ctx.strokeStyle = color;
      ctx.lineWidth = 3;
      ctx.beginPath();
      ctx.fillStyle = color;
      ctx.moveTo(this.x1, this.y1);
      ctx.lineTo(this.x2, this.y2);
      ctx.stroke();
    }
  }

  class Point {
    constructor(x, y, id, coordY, coordX) {
      this.x = x;
      this.y = y;
      this.coordX = coordX;
      this.coordY = coordY;
      this.radius = 1;
      this.id = id;
      this.hovered = false;
    }

    draw(color) {
      ctx.beginPath();
      ctx.fillStyle = color;
      ctx.strokeStyle = color;
      ctx.arc(this.x, this.y, this.radius, Math.PI * 2, 0, false);
      ctx.fill();
      ctx.stroke();
    }

    onClick(mousex, mousey) {
      if (gridIndexAmount > 20) {
        return;
      }
      const distance = Math.sqrt(
        Math.pow(this.x - mousex, 2) + Math.pow(this.y - mousey, 2)
      );
      const container = document.getElementById("container");
      const div = document.createElement("div");

      div.style.position = "fixed";
      div.style.left = mousex + (window.innerWidth * 30) / 100 + "px";
      div.style.top = mousey + "px";
      div.style.background = "#FFF";
      div.style.fontWeight = "600";
      div.style.border = "1px solid black";
      div.style.padding = "5px";
      div.style.borderRadius = "50px";
      div.id = this.id;

      const text = document.createTextNode(`[${this.coordX}, ${this.coordY}]`);

      if (distance < this.radius + 5) {
        if (!this.hovered) {
          div.append(text);
          container.appendChild(div);
          this.hovered = true;
        }
      } else {
        if (this.hovered == true) {
          if (document.getElementById(`${this.id}`)) {
            this.hovered = false;
            container.removeChild(document.getElementById(this.id));
          }
        }
      }
    }
  }

  const canvas = document.getElementById("canvas");
  const ctx = canvas.getContext("2d");

  // Clean on every new generation
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  var window_height = window.innerHeight;
  var window_width = window.innerWidth;

  window_height = window.innerHeight;
  window_width = window.innerWidth - (window.innerWidth * 30) / 100;
  canvas.height = window_height;
  canvas.width = window_width;
  let height = window_width - (window_width * 40) / 100;
  let width = window_width - (window_width * 40) / 100;
  var distance = width / (2 * gridIndexAmount + 2);
  var transformationX = width / 2;
  var transformationY = height;

  // Get the center of the screen in relation to a width and height
  function calculateCenter(x, y) {
    let centerPosX = window_width / 2 - x / 2;
    let centerPosY = window_height / 2 - y / 2;

    return [centerPosX, centerPosY];
  }

  ctx.fillStyle = "red";

  canvas.style.background = "#fff";

  const sqCenter = calculateCenter(width, height);

  var changeX = 0;
  var changeY = 0;

  function draw() {
    function transformY(changeY) {
      transformationX = transformationX + changeX;
      transformationY = transformationY - changeY;
      return transformationY;
    }

    var temp = distance;

    // DRAW LINES
    for (
      var i = -gridIndexAmount;
      i <= gridIndexAmount && i >= -gridIndexAmount;
      i++
    ) {
      ctx.beginPath();
      if (i === 0) {
        ctx.strokeStyle = "#000";
      } else {
        ctx.strokeStyle = "#bbb";
      }
      ctx.moveTo(sqCenter[0], temp + sqCenter[1]);
      ctx.lineTo(sqCenter[0] + transformY(changeY), sqCenter[1] + temp);
      temp = temp + distance;
      ctx.stroke();
    }
    temp = distance;

    for (
      var i = -gridIndexAmount;
      i <= gridIndexAmount && i >= -gridIndexAmount;
      i++
    ) {
      ctx.beginPath();

      if (i === 0) {
        ctx.strokeStyle = "#000";
      } else {
        ctx.strokeStyle = "#bbb";
      }
      ctx.moveTo(sqCenter[0] + temp, sqCenter[1]);
      ctx.lineTo(sqCenter[0] + temp, transformY(changeY) + sqCenter[1]);
      temp = temp + distance;
      ctx.stroke();
    }
    // END DRAW LINES

    var temp = distance;

    // ADD THE COORDINATES

    if (!gridIndexAmount > 20) {
      ctx.font = "15px Segoe UI";
      ctx.fillStyle = "#000";
      for (
        var i = gridIndexAmount;
        i >= -gridIndexAmount && i <= gridIndexAmount;
        i--
      ) {
        ctx.beginPath();
        ctx.fillText(i, sqCenter[0] - 30, sqCenter[1] + temp);
        ctx.stroke();
        temp = distance + temp;
      }

      temp = distance;

      for (
        var i = -gridIndexAmount;
        i <= gridIndexAmount && i >= -gridIndexAmount;
        i++
      ) {
        ctx.beginPath();
        ctx.fillText(i, sqCenter[0] + temp, sqCenter[1] - 30);
        ctx.stroke();
        temp = distance + temp;
      }
    }
    // END ADD THE COORDINATES

    temp = [distance, distance];

    let index = 0;

    // DRAW THE POINTS (IF THE GRID SIZE < 20)
    for (
      var i = gridIndexAmount;
      i >= -gridIndexAmount && i <= gridIndexAmount;
      i--
    ) {
      temp[1] = distance;
      for (
        let j = -gridIndexAmount;
        j <= gridIndexAmount && j >= -gridIndexAmount;
        j++
      ) {
        const circle = new Point(
          sqCenter[0] + temp[1],
          sqCenter[1] + temp[0],
          index,
          i,
          j
        );
        circles.push(circle);
        if (i == 0 && j == 0) {
          circle.draw("#F00");
        } else if (i == 0) {
          circle.draw("#000");
        } else if (j == 0) {
          circle.draw("#000");
        } else {
          circle.draw("#bbb");
        }
        temp[1] = distance + temp[1];
        index++;
      }
      temp[0] = distance + temp[0];
    }

    canvas.addEventListener("mousemove", (evt) => {
      const rect = canvas.getBoundingClientRect();
      const mousex = evt.clientX - rect.left;
      const mousey = evt.clientY - rect.top;

      circles.forEach((circle) => {
        circle.onClick(mousex, mousey);
      });
    });

    ctx.strokeStyle = "#000";
    ctx.strokeRect(sqCenter[0], sqCenter[1], width, height);
  }
  // END DRAW THE POINTS

  // DRAW VECTORS
  function drawVector(
    coordX,
    coordY,
    circles,
    color = generateRandomColors(1)[0]
  ) {
    const centerCircle = circles.find(
      (circle) => circle.coordX === 0 && circle.coordY === 0
    );
    console.log(centerCircle);
    circles.forEach((circle) => {
      if (circle.coordX == coordX && circle.coordY == coordY) {
        console.log(circle);
        var v = new Vector(
          centerCircle.x,
          centerCircle.y,
          circle.x,
          circle.y,
          circle.coordX,
          circle.coordY
        );
        v.draw(color);
        vectorList.push(v);
        return;
      }
    });
  }
  //END DRAW VECTORS

  // VECTOR CONTAINER
  const container = document.getElementById("vectorCreator");
  const vectors = document.getElementById("vectors");
  const addVectorBtn = document.createElement("button");
  const goCrazy = document.createElement("button");
  container.append(addVectorBtn);
  container.append(goCrazy);

  goCrazy.innerHTML = "Go Crazy";
  addVectorBtn.innerHTML = "Add Vector";
  // END VECTOR CONTAINER

  let isVectorAdded = false;
  addVectorBtn.addEventListener("click", () => {
    if (isVectorAdded == true) return;

    // Create inputs to handle vector position
    const inputX = document.createElement("input");
    inputX.type = "number";
    inputX.max = gridIndexAmount;
    inputX.min = -gridIndexAmount;
    inputX.defaultValue = 0;
    const textX = document.createElement("h4");
    textX.appendChild(document.createTextNode("X Axis:"));

    const inputY = document.createElement("input");
    inputY.type = "number";
    inputY.max = gridIndexAmount;
    inputY.min = -gridIndexAmount;
    inputY.defaultValue = 0;

    const textY = document.createElement("h4");
    textY.appendChild(document.createTextNode("Y Axis:"));
    container.appendChild(textX);
    container.appendChild(inputX);

    // Confirmation button
    const buttonConfirm = document.createElement("button");
    buttonConfirm.appendChild(document.createTextNode("Confirm"));
    container.appendChild(textY);
    container.appendChild(inputY);
    container.appendChild(buttonConfirm);

    // Create sum-all button
    const sumButton = document.createElement("button");

    const sumContainer = document.getElementById("sumContainer");
    sumButton.innerHTML = "Sum";
    // Check if confirm
    buttonConfirm.addEventListener("click", (event) => {
      sum = "";
      if (vectorList.length > 0) {
        // Add sum if there's more than one vector
        sum = document.createElement("p");
        sum.innerHTML = "+";
        sumContainer.appendChild(sumButton);
      }

      // Add vector draw
      drawVector(inputX.value, inputY.value, circles);
      // Add vector matrix representation
      const vectorMatrix = document.createElement("p");
      vectorMatrix.innerHTML = `[${inputX.value}; ${inputY.value}]`;
      vectors.append(sum);
      vectors.appendChild(vectorMatrix);
    });

    // Create the sum
    sumButton.addEventListener("click", (event) => {
      let finalX = 0;
      let finalY = 0;

      vectorList.forEach((vector) => {
        finalX += parseInt(vector.coordX);
        finalY += parseInt(vector.coordY);
      });

      console.log(finalX, finalY);
      const resultMatrix = document.createElement("p");
      resultMatrix.innerHTML = `= [${finalX}; ${finalY}]`;
      vectors.appendChild(resultMatrix);

      // Draw the result
      drawVector(finalX, finalY, circles, "red");
    });

    isVectorAdded = true;
  });

  // Go crazy function (Adds all possible vectors to the matrix)
  goCrazy.addEventListener("click", async (event) => {
    for (
      let i = -gridIndexAmount;
      i <= gridIndexAmount && i >= -gridIndexAmount;
      i++
    ) {
      setTimeout(function () {
        for (
          let j = -gridIndexAmount;
          j <= gridIndexAmount && j >= -gridIndexAmount;
          j++
        ) {
          function getRndInteger(min, max) {
            return Math.floor(Math.random() * (max - min + 1)) + min;
          }

          setTimeout(function () {
            colors = generateRandomColors(30);

            drawVector(
              i,
              j,
              circles,
              colors[getRndInteger(0, colors.length - 1)]
            );
          }, goCrazyGenDelay * Math.abs(j)); // Has a bit of delay
        }
      }, goCrazyGenDelay * Math.abs(i));
    }
  });

  draw();
  ctx.closePath();
}

function generateRandomColors(numColors) {
  const colors = [];
  for (let i = 0; i < numColors; i++) {
    const r = 0;
    const g = Math.floor(Math.random() * 256);
    const b = Math.floor(Math.random() * 256);
    const color = `rgb(${r}, ${g}, ${b})`;
    colors.push(color);
  }
  return colors;
}
```
