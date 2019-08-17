```html
<!-- 代码高亮 -->
<link href="https://cdn.bootcss.com/highlight.js/9.12.0/styles/atom-one-light.min.css" rel="stylesheet">
<!-- fira_code字体 -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/firacode@1.205.0/distr/fira_code.css">

<link rel="stylesheet" href="https://unpkg.com/prismjs@1.15.0/themes/prism-tomorrow.css">
<script src="https://unpkg.com/prismjs@1.15.0/prism.js"></script>

<!-- p5.js库 -->
<script src="https://unpkg.com/p5@0.7.2/lib/p5.min.js"></script>
<script src="https://unpkg.com/p5@0.7.2/lib/addons/p5.dom.min.js"></script>

    <script>
      // canvas 动画
      const sketch_2_ctx = p => {
        p.circles = [];
        p.img;
        p.spots = [];
        p.imgPath = 'https://i.loli.net/2018/09/05/5b8fce8bbe8b5.jpg';
        p._w = 600;
        p._pgColor = p.color(255, 255, 255);
        p.w;
        p.h;

        p.preload = () => {
          p.img = p.loadImage(p.imgPath);
          p.img.resize(100, 100);
        };

        p.setup = () => {
          p.w = p.min(p._w, p.windowWidth);
          p.h = p.w / 1.5;
          let canvas = p.createCanvas(p.w, p.h);
          canvas.style('position', 'fixed');
          canvas.style('left', '0');
          canvas.style('top', '0');
          canvas.style('z-index', '-1');
          p.pixelDensity(1);
          p.img.loadPixels();

          for (let x = 0; x < p.img.width; x++) {
            for (let y = 0; y < p.img.height; y++) {
              let index = (x + y * p.img.width) * 4;
              let c = p.img.pixels[index];
              let b = p.brightness(p.color(c));
              if (b > 10) {
                p.spots.push(p.createVector(x, y));
              }
            }
          }
        };

        p.windowResized = () => {
          let nw = p.min(p._w, p.windowWidth);
          let nh = nw / 1.5;
          p.resizeCanvas(nw, nh);
          p.background(p._pgColor);
        };

        p.draw = () => {
          p.background(p._pgColor);
          p.scale(p.map(p._w - p.width, 0, p._w, 1, 0));
          for (let i = 0; i < 5; i++) {
            let newC = newCircle();
            if (newC !== null) {
              p.circles.push(newC);
            }
          }
          for (let c of p.circles) {
            if (c.growing) {
              if (c.edges()) {
                c.growing = false;
              } else {
                for (let other of p.circles) {
                  if (c === other) continue;
                  let d = p.dist(c.x, c.y, other.x, other.y);
                  if (d - 2 < c.r + other.r) {
                    c.growing = false;
                    break;
                  }
                }
              }
            }
            c.run();
          }

          if (p.circles.length > 800) {
            p.noLoop();
          }
        };

        function newCircle() {
          let r = p.int(p.random(0, p.spots.length));
          let spot = p.spots[r];
          let x = spot.x;
          let y = spot.y;

          let valid = true;
          for (let c of p.circles) {
            let d = p.dist(x, y, c.x, c.y);
            if (d < c.r) {
              valid = false;
              break;
            }
          }

          if (valid) {
            let strokeC = p.color(p.random(255), p.random(255), p.random(255));
            return new Circle(x, y, 1, strokeC);
          } else {
            return null;
          }
        }

        class Circle {
          constructor(x, y, r, strokeColor = 255) {
            this.x = x;
            this.y = y;
            this.r = r;
            this.strokeColor = strokeColor;

            this.growing = true;
          }

          run() {
            this.grow();
            this.show();
          }

          show() {
            p.noFill();
            p.strokeWeight(2);
            p.stroke(this.strokeColor);
            p.ellipse(this.x, this.y, this.r * 2);
          }

          grow() {
            if (this.growing) {
              this.r += 0.5;
            }
          }

          edges() {
            // this.r > 12 是后面填上去的
            return (
              this.x + this.r > p.width ||
              this.x - this.r < 0 ||
              this.y + this.r > p.height ||
              this.y - this.r < 0 ||
              this.r > 12
            );
          }
        }
      };
      const p5_2 = new p5(sketch_2_ctx);
    </script>
```