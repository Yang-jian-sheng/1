<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>烟花效果</title>
    <style>
        /* 设置页面样式 */
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden; /* 隐藏滚动条 */
            background-color: #000; /* 黑色背景 */
        }

        canvas {
            display: block; /* 使 canvas 占满整个页面 */
        }
    </style>
</head>
<body>
    <canvas id="fireworks"></canvas>
    <script>
        const canvas = document.getElementById('fireworks');
        const ctx = canvas.getContext('2d');

        // 设置 canvas 大小
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        // 烟花粒子类
        class Particle {
            constructor(x, y, color) {
                this.x = x;
                this.y = y;
                this.color = color;
                this.velocity = {
                    x: (Math.random() - 0.5) * 5,
                    y: (Math.random() - 0.5) * 5
                };
                this.alpha = 1; // 初始透明度
                this.size = Math.random() * 3 + 1; // 粒子大小
            }

            draw() {
                ctx.save();
                ctx.globalAlpha = this.alpha;
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                ctx.fillStyle = this.color;
                ctx.fill();
                ctx.restore();
            }

            update() {
                this.x += this.velocity.x;
                this.y += this.velocity.y;
                this.alpha -= 0.01; // 逐渐消失
                this.size -= 0.02; // 逐渐变小
            }
        }

        // 烟花类
        class Firework {
            constructor(x, y) {
                this.x = x;
                this.y = y;
                this.particles = [];
                this.color = `hsl(${Math.random() * 360}, 50%, 50%)`; // 随机颜色
                for (let i = 0; i < 100; i++) {
                    this.particles.push(new Particle(this.x, this.y, this.color));
                }
            }

            update() {
                this.particles.forEach((particle, index) => {
                    if (particle.alpha <= 0) {
                        this.particles.splice(index, 1); // 移除完全消失的粒子
                    } else {
                        particle.update();
                    }
                });
            }

            draw() {
                this.particles.forEach(particle => particle.draw());
            }
        }

        // 烟花数组
        const fireworks = [];

        // 动画循环
        function animate() {
            ctx.fillStyle = 'rgba(0, 0, 0, 0.1)'; // 背景渐隐效果
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            fireworks.forEach((firework, index) => {
                firework.update();
                firework.draw();
                if (firework.particles.length === 0) {
                    fireworks.splice(index, 1); // 移除完全消失的烟花
                }
            });

            requestAnimationFrame(animate);
        }

        // 鼠标点击触发烟花
        canvas.addEventListener('click', (e) => {
            fireworks.push(new Firework(e.clientX, e.clientY));
        });

        // 自动触发烟花
        setInterval(() => {
            const x = Math.random() * canvas.width;
            const y = Math.random() * canvas.height;
            fireworks.push(new Firework(x, y));
        }, 1000);

        // 启动动画
        animate();
    </script>
</body>
</html>
