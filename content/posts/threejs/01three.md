---
title: "01three.js"
date: 2021-12-16T09:09:40+09:00
draft: true
---

# Three.js学習

## Three.js一般初期設定

`scene`, `camera`, `renderer`
の定義

`scene`
  ->  表示したいすべての光源を保持して変更を監視するコンテナオブジェクト

`camera`
  ->  シーンを描画するときに何が見えるかを決定する．

`renderer`
  ->  cameraオブジェクトの角度に基づいてブラウザ内でsceneオブジェクトがどのように見えるかを計算する

```javascript
var scene = new THREE.Scene();

var camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);

var renderer = new THREE.WebGLRenderer();
renderer.setClearColor(new THREE.Color(0xEEEEEE));
renderer.setSize(window.innerWidth, window.innerHeight);
```

## axesオブジェクト生成 

![axesとは](https://raw.githubusercontent.com/EveSquare/images/main/axes.png?token=AMA45GTAYH25JCXWNGUO6JDBXGNHY)
赤，青，緑の線のこと

```javascript
var axes = new THREE.AxisHelper(60); //60は長さ
scene.add(axes);
```

## 平面を作成

![平面を作成](https://raw.githubusercontent.com/EveSquare/images/main/axes.png?token=AMA45GTAYH25JCXWNGUO6JDBXGNHY)
灰色の薄っぺらい平面のこと

```javascript
var planeGeometry = new THREE.PlaneGeometry(<幅>, <高さ>);
var planeMaterial = new THREE.MeshBasicMaterial({color: 0xcccccc}); //見た目の設定
var plane = new THREE.Mesh(planeGeometry, planeMaterial); //2つを組み合わせてplaneというMeshオブジェクトにする
```
#### 正しい位置に配置する
```javascript
//x軸周りに90度回転
plane.rotation.x = -0.5 * Math.PI;
plane.position.x = 15;
plane.position.y = 0;
plane.position.z = 0;

scene.add(plane);
```

# cudeオブジェクト,qsphereオブジェクト

マテリアルのwireframeプロパティがtrueに設定されているため，Three.jsはソリッドなオブジェクトではなく，ワイヤーフレームを描画します．


<small>※プログラムはオライリージャパン出版の「初めてのThree.js」から引用</small>