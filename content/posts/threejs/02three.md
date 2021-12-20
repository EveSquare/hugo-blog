---
title: "02three"
date: 2021-12-20T22:33:18+09:00
draft: true
---

# アニメーション

![最終状態](/images/three/02/02threefin.png)

# 環境構築

HTMLに直で書くのは型がなくてつらいため，ここから環境を変更した．
環境構築には以下のサイトを参考にした．[webpack+TypeScript+Three.jsの最小構成](https://ics.media/entry/16329/)

これにより，ホットリロードでTypeScriptが使える環境となった．

### 追加したライブラリ

- Stats(three.jsを入れた時に付属) 
        
        再生しているフレームレートを確認できるライブラリ

    ```typescript
    import Stats from 'three/examples/jsm/libs/stats.module';
    ```

- [dat.gui](https://github.com/dataarts/dat.gui)

        アニメーションや座標などのデバッグを容易にするライブラリ <small>Googleが作っている..？</small>

    ```typescript
    $ npm install --save dat.gui
    ~~~
    import * as dat from 'dat.gui';
    ```

# キューブアニメーションの実現

![キューブアニメーション](/images/three/02/cube_animation.gif)

キューブを回すアニメーションを記述した．

`renderScene()`という関数を容易してその関数内に`requestAnimationFrame`という関数を呼び出すことで，アニメーションを描画することができる．
```typescript
renderScene();

function renderScene() {
    //立方体の回転
    cube.rotation.x += 0.02;
    cube.rotation.y += 0.02;
    cube.rotation.z += 0.02;
    ~~~~~~~
    requestAnimationFrame(renderScene);
    renderer.render(scene, camera);
}
```
以上のコードで上にアニメーションが実現出来た．
ここでのポイントは`rotation.x | y | z`である．回すときは`rotation`プロパティを用いる．

# 円(Sphere)の運動アニメーション

![円(Sphere)の運動アニメーション](/images/three/02/sphere_animation.gif)

円の運動アニメーションを実現する為，以下のコードを記述した．
```typescript
var step = 0;
renderScene();

function renderScene() {
    ~~~~~~
    //球のバウンド
    step += 0.02;
    sphere.position.x = 20 + ( 10 * (Math.cos(step)));
    sphere.position.y = 2 + ( 10 * Math.abs(Math.sin(step)));
    requestAnimationFrame(renderScene);
    renderer.render(scene, camera);
}
```
同じく`renderScene()`内に記述した．円が円運動をするため`Math.cos | sin`などを用いて計算している．

また，`step`変数は運動速度のパラメータで大きければ大きい程速くなる．

![円(Sphere)の運動アニメーション](/images/three/02/sphere2_animation.gif)

速度が上昇していることが確認できる．

# Stats

一番上に載っている画像右上のフレームレートを表示させる．

```typescript
var stats = initStats();
~~~~~
function initStats() {
    var stats = new (<any>Stats)();
    stats.setMode(0); // 0: fps, 1: ms
    stats.domElement.style.position = 'absolute';
    stats.domElement.style.left = '0px';
    stats.domElement.style.top = '0px';
    document.getElementById("Stats-output")!.appendChild(stats.domElement);
    return stats;
}
```

# dat.gui

同じく左上に表示されているシークバーを表示する．
```typescript
//変更する値を定義
const controls = {
    rotationSpeed: 0.02,
    bouncingSpeed: 0.02
}

//シークバーを表示
const gui = new dat.GUI();
gui.add(controls, 'rotationSpeed', 0, 0.5);
gui.add(controls, 'bouncingSpeed', 0, 0.5);
~~~//以下のように使用する．
cube.rotation.x += controls.rotationSpeed;
cube.rotation.y += controls.rotationSpeed;
cube.rotation.z += controls.rotationSpeed;
```
## 余談

TypeScriptでコードを書くにあたってDOM操作をしようとするとなんかエラー出る時の対処法．

```typescript
document.getElementById("Stats-output")!.appendChild(stats.domElement);
```

getElementById~の後に「!」を付けることでエラーなく動作する．
詳しくはWebで
