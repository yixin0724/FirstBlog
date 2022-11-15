---
title: love
date: 2022-09-21 22:18:06
type: "love"
---







<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>最爱芳雅宝贝</title>
    <style>
        html,
        body {
            height: 100%;
            padding: 0;
            margin: 0;
            background: #000;
        }

        canvas {
            position: absolute;
            width: 100%;
            height: 100%;
        }
        
        .down {
            position: absolute;
            top: 50%;
        }
        
        p1 {
            color: pink;
            margin-left: 710px;
            font-size: 35px;
        }
    </style>
</head>

<body>
    <canvas id="pinkboard"></canvas>
    <font size="6" color="pink">
        <marquee direction="down" scrollamount=5 class="down"></marquee>
        <marquee direction="left" scrollamount=5></marquee>
        <marquee direction="right" scrollamount=5></marquee>
    </font>
    <p1>芳雅宝贝</p1>
    <script>
        var settings = {
            particles: {
                length: 500,
                duration: 2,
                velocity: 100,
                effect: -0.75,
                size: 30,
            },
        };
        (function() {
            var b = 0;
            var c = ["ms", "moz", "webkit", "o"];
            for (var a = 0; a < c.length && !window.requestAnimationFrame; ++a) {
                window.requestAnimationFrame = window[c[a] + "RequestAnimationFrame"];
                window.cancelAnimationFrame = window[c[a] + "CancelAnimationFrame"] || window[c[a] + "CancelRequestAnimationFrame"]
            }
            if (!window.requestAnimationFrame) {
                window.requestAnimationFrame = function(h, e) {
                    var d = new Date().getTime();
                    var f = Math.max(0, 16 - (d - b));
                    var g = window.setTimeout(function() {
                        h(d + f)
                    }, f);
                    b = d + f;
                    return g
                }
            }
            if (!window.cancelAnimationFrame) {
                window.cancelAnimationFrame = function(d) {
                    clearTimeout(d)
                }
            }
        }());
        var Point = (function() {
            undefined

            function Point(x, y) {
                undefined
                this.x = (typeof x !== 'undefined') ? x : 0;
                this.y = (typeof y !== 'undefined') ? y : 0;
            }
            Point.prototype.clone = function() {
                undefined
                return new Point(this.x, this.y);
            };
            Point.prototype.length = function(length) {
                undefined
                if (typeof length == 'undefined')
                    return Math.sqrt(this.x * this.x + this.y * this.y);
                this.normalize();
                this.x *= length;
                this.y *= length;
                return this;
            };
            Point.prototype.normalize = function() {
                undefined
                var length = this.length();
                this.x /= length;
                this.y /= length;
                return this;
            };
            return Point;
        })();
        var Particle = (function() {
            undefined
    
            function Particle() {
                undefined
                this.position = new Point();
                this.velocity = new Point();
                this.acceleration = new Point();
                this.age = 0;
            }
            Particle.prototype.initialize = function(x, y, dx, dy) {
                undefined
                this.position.x = x;
                this.position.y = y;
                this.velocity.x = dx;
                this.velocity.y = dy;
                this.acceleration.x = dx * settings.particles.effect;
                this.acceleration.y = dy * settings.particles.effect;
                this.age = 0;
            };
            Particle.prototype.update = function(deltaTime) {
                undefined
                this.position.x += this.velocity.x * deltaTime;
                this.position.y += this.velocity.y * deltaTime;
                this.velocity.x += this.acceleration.x * deltaTime;
                this.velocity.y += this.acceleration.y * deltaTime;
                this.age += deltaTime;
            };
            Particle.prototype.draw = function(context, image) {
                undefined
    
                function ease(t) {
                    undefined
                    return (--t) * t * t + 1;
                }
                var size = image.width * ease(this.age / settings.particles.duration);
                context.globalAlpha = 1 - this.age / settings.particles.duration;
                context.drawImage(image, this.position.x - size / 2, this.position.y - size / 2, size, size);
            };
            return Particle;
        })();
        var ParticlePool = (function() {
            undefined
            var particles,
                firstActive = 0,
                firstFree = 0,
                duration = settings.particles.duration;
    
            function ParticlePool(length) {
                undefined
                particles = new Array(length);
                for (var i = 0; i < particles.length; i++)
                    particles[i] = new Particle();
            }
            ParticlePool.prototype.add = function(x, y, dx, dy) {
                undefined
                particles[firstFree].initialize(x, y, dx, dy);
                firstFree++;
                if (firstFree == particles.length) firstFree = 0;
                if (firstActive == firstFree) firstActive++;
                if (firstActive == particles.length) firstActive = 0;
            };
            ParticlePool.prototype.update = function(deltaTime) {
                undefined
                var i;
                if (firstActive < firstFree) {
                    undefined
                    for (i = firstActive; i < firstFree; i++)
                        particles[i].update(deltaTime);
                }
                if (firstFree < firstActive) {
                    undefined
                    for (i = firstActive; i < particles.length; i++)
                        particles[i].update(deltaTime);
                    for (i = 0; i < firstFree; i++)
                        particles[i].update(deltaTime);
                }
                while (particles[firstActive].age >= duration && firstActive != firstFree) {
                    undefined
                    firstActive++;
                    if (firstActive == particles.length) firstActive = 0;
                }
            };
            ParticlePool.prototype.draw = function(context, image) {
                undefined
                if (firstActive < firstFree) {
                    undefined
                    for (i = firstActive; i < firstFree; i++)
                        particles[i].draw(context, image);
                }
                if (firstFree < firstActive) {
                    undefined
                    for (i = firstActive; i < particles.length; i++)
                        particles[i].draw(context, image);
                    for (i = 0; i < firstFree; i++)
                        particles[i].draw(context, image);
                }
            };
            return ParticlePool;
        })();
        (function(canvas) {
            undefined
            var context = canvas.getContext('2d'),
                particles = new ParticlePool(settings.particles.length),
                particleRate = settings.particles.length / settings.particles.duration,
                time;
    
            function pointOnHeart(t) {
                undefined
                return new Point(
                    160 * Math.pow(Math.sin(t), 3),
                    130 * Math.cos(t) - 50 * Math.cos(2 * t) - 20 * Math.cos(3 * t) - 10 * Math.cos(4 * t) + 25
                );
            }
            var image = (function() {
                undefined
                var canvas = document.createElement('canvas'),
                    context = canvas.getContext('2d');
                canvas.width = settings.particles.size;
                canvas.height = settings.particles.size;
    
                function to(t) {
                    undefined
                    var point = pointOnHeart(t);
                    point.x = settings.particles.size / 2 + point.x * settings.particles.size / 350;
                    point.y = settings.particles.size / 2 - point.y * settings.particles.size / 350;
                    return point;
                }
                context.beginPath();
                var t = -Math.PI;
                var point = to(t);
                context.moveTo(point.x, point.y);
                while (t < Math.PI) {
                    undefined
                    t += 0.01;
                    point = to(t);
                    context.lineTo(point.x, point.y);
                }
                context.closePath();
                context.fillStyle = '#ea80b0';
                context.fill();
                var image = new Image();
                image.src = canvas.toDataURL();
                return image;
            })();
    
            function render() {
                undefined
                requestAnimationFrame(render);
                var newTime = new Date().getTime() / 1000,
                    deltaTime = newTime - (time || newTime);
                time = newTime;
                context.clearRect(0, 0, canvas.width, canvas.height);
                var amount = particleRate * deltaTime;
                for (var i = 0; i < amount; i++) {
                    undefined
                    var pos = pointOnHeart(Math.PI - 2 * Math.PI * Math.random());
                    var dir = pos.clone().length(settings.particles.velocity);
                    particles.add(canvas.width / 2 + pos.x, canvas.height / 2 - pos.y, dir.x, -dir.y);
                }
                particles.update(deltaTime);
                particles.draw(context, image);
            }
    
            function onResize() {
                undefined
                canvas.width = canvas.clientWidth;
                canvas.height = canvas.clientHeight;
            }
            window.onresize = onResize;
            setTimeout(function() {
                undefined
                onResize();
                render();
            }, 10);
        })(document.getElementById('pinkboard'));
    </script>
</body>

</html>
