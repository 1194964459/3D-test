# three.js 与 Unity比较

unity中有哪些核心对象？分别都有什么用？为什么我们的webAR工程会参考Unity中的各种对象、名词来设计？
https://www.doubao.com/thread/w7a79c33af7073f12

## three.js与unity如何协作？
Unity：负责复杂 3D 内容的制作（如高精度模型、动画、物理模拟）、资源烘焙（如光照贴图、碰撞体数据）。
Three.js：负责在 Web 端轻量化展示 Unity 制作的内容（如产品预览、场景漫游），或接收 Unity 的实时数据进行可视化。


## Unity 中的核心对象及其作用
**1. 场景（Scene）**：   
3D 内容的 “容器”，包含模型、灯光、相机等..

**2. 游戏对象（GameObject）**：  
场景中所有实体的基类，Unity 中 “一切皆为 GameObject”—— 无论是 3D 模型、灯光、相机，还是逻辑控制器，都是 GameObject。

**3. 组件（Component）：**  
具体功能的 “模块”，常见核心组件：
* Transform：每个 GameObject 必有的组件，管理位置（Position）、旋转（Rotation）、缩放（Scale），是 3D 空间定位的基础。
* Renderer：控制 GameObject 的渲染（如 MeshRenderer 渲染 3D 模型，SpriteRenderer 渲染 2D 精灵），决定物体是否可见及外观。
* Collider：碰撞体组件（如 BoxCollider、SphereCollider），用于检测物体间的碰撞（如角色碰到墙壁会阻挡）。
* Rigidbody：物理组件，使物体遵循物理规律（如重力、碰撞反弹），实现真实的运动模拟。
* Camera：相机组件，定义场景的 “视角”，决定玩家能看到什么（类似现实中的摄像机）。
* Light：灯光组件（如 DirectionalLight 平行光、PointLight 点光），影响场景的光照和阴影，提升真实感。
* Script：自定义脚本组件（C# 编写），通过代码实现逻辑（如角色移动、按钮点击事件）。
* Animation：动画

**4. 资源（Asset）**
场景外的 “素材库”，包括模型（Model）、纹理（Texture）、音频（AudioClip）、脚本（Script）、动画（AnimationClip）等，

**5. 组件系统（Component System）**  
构建任意功能的实体。例如：
* 一个 “可交互的门”= GameObject + Transform（位置） + MeshRenderer（外观） + BoxCollider（碰撞检测） + 自定义脚本（开门逻辑）。
* 一个 “会下落的球”= GameObject + Transform + SphereRenderer + SphereCollider + Rigidbody（重力）。

**6. 生命周期（Lifecycle）**
作用：定义脚本组件的执行顺序（如 Start() 初始化、Update() 每帧更新、OnCollisionEnter() 碰撞触发），是逻辑驱动的基础。

## Three.js中的动画
样例：[Animation](../demo/Animation.html)

Three.js中3D 模型动画有3个核心组件：  
**1. AnimationClip（动画剪辑）**   
一段 “完整的动画片段，记录了模型在动画过程中，所有需要变化的属性（如位置、旋转、缩放）的关键帧信息。

通常从模型文件（如 GLTF/GLB）中加载得到（代码中 gltf.animations[0] 就是取模型的第一个动画剪辑），也可以在代码中手动创建。

**2. AnimationMixer（动画混合器）**   
动画的 “播放器” 和 “管理器”，负责驱动模型的动画播放，还能实现多个动画的混合/过渡（比如让角色同时 “走路” 和 “挥手”）。

**关联模型**：每个 AnimationMixer 绑定到一个具体的模型（代码中 `new THREE.AnimationMixer(model) 表示该混合器驱动 model 这个模型的动画`）。

核心能力：
* 控制动画的播放、暂停、停止。
* 管理动画的时间缩放（快放/慢放）。
* 混合多个 AnimationAction，实现复杂的动画叠加效果。

**3. AnimationAction（动画动作）**  
如何创建？`const action = mixer.clipAction(clip)`

* 控制 “某一段动画的具体播放行为”。如：播放（action.play()）、暂停（action.pause()）、停止（action.stop()
* 监听动画事件（如 'finished' 事件，在动画播放结束时触发）。
* 调整动画的权重、时间缩放等参数，实现更精细的控制。

示例：加载模型后控制模型的播放
```js
// 1. 加载模型，得到 gltf 对象（包含 animations 等数据）
loader.load('yourModel.glb', (gltf) => {
  const model = gltf.scene;
  scene.add(model);

  // 2. 创建 AnimationMixer，绑定到模型
  const mixer = new THREE.AnimationMixer(model);

  // 3. 从模型中取第一个 AnimationClip
  const clip = gltf.animations[0];

  // 4. 为该 AnimationClip 创建 AnimationAction
  const action = mixer.clipAction(clip);

  // 5. 监听 AnimationAction 的 'finished' 事件（动画结束时触发）
  action.addEventListener('finished', () => {
    console.log('动画播放完成');
  });

  // 6. 播放动画
  action.play();
});
```