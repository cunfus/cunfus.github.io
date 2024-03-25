# Manim


## 安装
manim 的环境配置很麻烦，直接上社区版 docker 镜像

```shell
docker run -it --name my-manim-container -v ".:/manim" manimcommunity/manim /bin/bash

docker start my-manim-container

docker exec -it my-manim-container manim -ql scene.py CircleToSquare
```

## 常用命令

- `-a` 渲染所有类
- `-p` 渲染场景后播放
- `-f` 渲染场景后打开动画所在目录
- `-s` 保存最后一帧为 image
- `--save_sections` 剪辑片段
- `-ql` 低质量渲染 480p 15 frame/s
- `-qm` 中质量渲染 720p 30 frame/s
- `-qh` 高质量渲染 1080p 60 frame/s
- `-qk` k 级质量渲染 2160p 60 frame/s
- `--format=gif` 动画使用 gif 格式保存 (默认 mp4)

## 概念

Mobject - Animation - Scene

任何可以在屏幕上显示的对象都是 Mobject。

对象可以是 Circle Square Triangle

对象的属性 set_fill set_stroke

对象的移动 shift rotate move_to next_to align_to

对象的展示 add remove wait  animate.shift animate.rotate FadeIn 
Rotate FadeOut
