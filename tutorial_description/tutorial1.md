# config는 게임 구성 설정을 한다

- config 개체는 게임을 구성한다. 렌더러, 치수 및 기본 씬 등을 설정할 수 있다.

```jsx
const config = { config = {
    type: Phaser.AUTO,: Phaser.AUTO,
    width: 800,: 800,
    height: 600,: 600,
    scene: {: {
        preload: preload,: preload,
        create: create,: create,
        update: update: update
    }}
};};
```

- `Phaser.Game` 개체에 `config`를 적용하면 프로세스가 시작된다.

```jsx
var game = new Phaser.Game(config);
```

- 이렇게 하면 Phaser에 생명을 불어넣는 프로세스가 시작됩니다.
- `type` 속성은 `Phaser.CANVAS`, `Phaser.WEBGL` 또는 `Phaser.AUTO`가 될 수 있다.
- 이것은 게임에 사용하려는 렌더링 컨텍스트입니다.

---

# assets 로딩하기

- 우리는 preload메소드 안에서 필요한 assets를 불러올 수 있다.
- this.load.image('key값', '이미지 경로')
- key값을 js에서 불러와서 사용할 수 있다.

```jsx
function preload ()
{
    this.load.image('sky', 'assets/sky.png');
    this.load.image('ground', 'assets/platform.png');
    this.load.image('star', 'assets/star.png');
    this.load.image('bomb', 'assets/bomb.png');
    this.load.spritesheet('dude', 
        'assets/dude.png',
        { frameWidth: 32, frameHeight: 48 }
    );
}
```

# 이미지 보기

- 이미지가 로드된 장소를 나타내기 위해서는 create 함수에서 이 이미지들을 위치시켜야 한다.
- this.add.image(x축, y축, '이미지 키값 ')

# 빌드하기

- this.add.image로 이미지 객체를 만들고 최근 장면에 나타낼 수 있다.
- 이미지를 어디든지 둘 순 있지만, 이미지를 화면안에 두도록 하자.
- Scene는 어느 방향으로든 확장할 수 있으며 크기 또한 고정되지 않는다.
- 그러므로 카메라를 이용해서 화면을 조정하도록 하자.
- 자아~, 장면에 배경이미지나 여러가지 플렛폼을 추가해보자!
- 먼저 config에 physics 개체 설정을 추가한다.

```jsx
const config = {
  // 추가
  physics: {
      default: 'arcade',  //아케이드
      arcade: {  
        gravity: { y: 300 },  //아케이드 중력 설정
        debug: false  //?
      }
  }
}
```

- create 함수에서 장면 설정을 한다.
- [this.physics.add.staticGroup](https://photonstorm.github.io/phaser3-docs/Phaser.Physics.Arcade.StaticGroup.html) : 이 함수를 사용해 추가된 객체들은 몸체가 없는경우(?) 기본 몸체가 부여됩ㄴ;디ㅣㅣ?

```jsx
//create에서 플렛폼(걍 이미지 이름)
function create ()
  {
      this.add.image(400, 300, 'sky');  //이미지 추가
      this.add.image(400, 300, 'star'); //이미지 추가
      platforms = this.physics.add.staticGroup(); //그룹만들기!

	    //그룹에 여러가지 이미지를 생성하고 추가하기 -> 하나의 이미지 그룹임!
      platforms.create(400, 568, 'ground').setScale(2).refreshBody(); 
      platforms.create(600, 400, 'ground');
      platforms.create(50, 250, 'ground');
      platforms.create(750, 220, 'ground');
      
  }
```

![스크린샷 2021-09-04 오후 10.28.22.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/41565a66-5007-4dc9-b8de-25db5810517f/스크린샷_2021-09-04_오후_10.28.22.png)

---

# 플렛폼 만들기

몸체(바디)는 하나의 사물 개체를 말하는 듯?

- 우리는 create 함수에서 코드를 추가해서 원하는 개체를 추가할 수 있다.
```jsx
platforms = this.physics.add.staticGroup(); //그룹만들기!
```

- 앞서 사용한 이 함수는 정적 물리 그룹을 생성한다.
- 아케이드 물리엔진에는 크게 정적 그리고 동적 물리 본체가 있다.
- 동적 몸체
    - 속도 혹은 가속도와 같은 힘을 통해 이동할 수 있는 몸체(물체)이다. <br/>
    - 우리가 실생활에서 볼 수 있는 충돌, 질량 법칙 등에 영향을 받는다. <br/>
- 정적 본체
    - 단순히 위치, 크기만 지정되어 있다. <br/>
    - 중력의 영향 혹은 무언가가 충돌해도 움직이지 않는다. <br/>
    - 이 정적 본체의 경우, 플레이어가 뛰어다니는 지면을 만들기에 적합하닷! <br/>
    - 아래 코드는 정적 본체이며, `staticGroup객체.create(너비, 높이, '이미지 키값')`으로 쓴다. <br/>
    - 만약 이 정적 본체에 추가한 이미지를 확대하고 싶다면 `setScale(2)`를 사용한다. <br/>
    - 그리고 이처럼 정적 본체의 **크기를 조정하면(확대 축소 등), `refreshBody`를 꼭 해줘야 한다!** <br/>
    (물리 세계에게 `**refreshBody`로** 정적 본체를 변형했다는 걸 꼬옥!!! 알려줘야 함!)
    
    <br/> 
    
    ```jsx
    function create ()
      {
          platforms.create(400, 568, 'ground').setScale(2).refreshBody();
          platforms.create(600, 400, 'ground');
          platforms.create(50, 250, 'ground');
          platforms.create(750, 220, 'ground');
          
      }
    ```
---

# 플레이어를 준비하자!

- 플레이어 이미지의 경우 단일 이미지가 아닌 아래와 같이 여러 동작을 포함한 이미지이다.
- 그러므로 이미지가 아닌 `spritesheet`로 로드해주어야ㅏㅏㅏ 한다ㅏㅏㅏㅏㅏ

(애니메이션 프레임이 포함되어 있는 이미지임)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/34ca8540-7b9a-47e6-8819-16786d1d6a4d/Untitled.png)

- create함수에서 player를 만들어보자!
- `this.add.image` 와 `this.load.spritesheet`의 차이는 sprite는 애니메이션을 부여할 수 있는 점이다.
- 만약 플레이어와 같이 사용자의 인터렉션에 의해 애니메이션이 동작해야하는 이미지의 경우, `this.load.spritesheet`로 이미지 개체를 만들고, `this.physics.add.sprite(x축, y축, 이미지키값)`을 사용해 화면에 추가해줘야한다.

```jsx
function create() {
  //플레이어 추가하기
  player = this.physics.add.sprite(100, 450, 'dude'); 
  //x축 100, y축 450에 dude이미지를 화면에 추가함
  ...
```

- `this.physics.add.sprite`를 사용한다는 건, 결국 동적 몸체를 사용한다는 말이 된다.

- `setBounce`는 점프 후 착지할 때 약간의 바운스를 준다.

```jsx
...
player.setBounce(0.2);
```

- 플레이어 몸체에 중력을 얼만큼 줄 건지 설정한다.
- 값이 높을 수록 물체가 무거워지고 더 빨리 떨어진다.

```jsx
...
player.body.setGravityY(300);
```

- 이 플레이어 개체가 어떤 지점에서 바운스할건지 설정한다.
- true를 하면 충돌하는 개체를 기준으로 바운스를 한다.

```jsx
...
player.setCollideWorldBounds(true);
```

# 플레이어의 애니메이션을 지정하자(1)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/34ca8540-7b9a-47e6-8819-16786d1d6a4d/Untitled.png)

- 보시다시피 이미지는 9개의 프레임이 있다.
- 좌측 4개는 왼쪽 이동을 위한 프레임, 우측 4개는 오른쪽 이동을 위한 프레임이다.

*(사실 요즘은 좌 우 프레임을 지정하지 않고, 한 방향만 만들고 뒤집어서 사용함)*

## 이동하기

- `this.anims.create()`를 사용해 애니메이션(동작 인터렉션)을 만들 수 있다.
    - key: 키보드 키
    - frames: start~end까지의 이미지 프레임을 쓰겠다~
    - frameRate: 초당 10프레임으로 실행함
    - repeat: -1을 하면 반복함

```jsx
this.anims.create({
    key: 'left',
    frames: this.anims.generateFrameNumbers('dude', { start: 0, end: 3 }),
    frameRate: 10,
    repeat: -1
});
```

```jsx
this.anims.create({
    key: 'right',
    frames: this.anims.generateFrameNumbers('dude', { start: 5, end: 8 }),
    frameRate: 10,
    repeat: -1
});
```

```jsx
this.anims.create({
    key: 'turn',
    frames: [ { key: 'dude', frame: 4 } ],
    frameRate: 20
});
```

- this.physics.add.collider(대상, 충돌대상)
- 이 코드를 사용하여 플레이어가 platforms과 충돌하게 한다.
- 이렇게 설정하면 플레이어가 platforms위에 있을 수 있다.

```jsx
this.physics.add.collider(player, platforms);
```

![스크린샷 2021-09-04 오후 10.54.20.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bfb63757-5cb2-485f-b781-5818a50e026a/스크린샷_2021-09-04_오후_10.54.20.png)

---

# 키보드로 컨트롤하잣!

- update함수에서 설정한다.
- `this.input.keyboard.createCursorKeys()`를 사용하여 키보드 이벤트를 설정할 수 있다.
- `setVelocityX(값)`은 몸체의 수평 속도를 지정할 수 있다.
- `player.anims.play('anims키값', true)`을 사용하여 사용한 애니메이션을 지정할 수 있다.

```jsx
function update ()
  {
    cursor = this.input.keyboard.createCursorKeys();
    if (cursor.left.isDown) {
        player.setVelocityX(-160);
        player.anims.play('left', true);
    }else if (cursor.right.isDown) {
        player.setVelocityX(160);
        player.anims.play('right', true);
    }
   ...

  }
```

- 하지만 위 두 개의 키값만 지정하면 플레이어몸체가 끝도없이 움직인다;;
- 위 두개의 키 동작 이외에는 멈춰야 하므로 아래와 같이 속도를 0으로 지정해준다.

```jsx
function update ()
  ...
  else {
        player.setVelocityX(0);
        player.anims.play('turn');
    }
   ...

  }
```

- 점프의 경우 setVelocityY를 사용하면 되는데, 플레이어 몸체는 중력을 받고 있으므로 업 이벤트만 지정해주면 된다.

```jsx
function update () {
 ...
  if (cursor.up.isDown && player.body.touching.down)
  {
      player.setVelocityY(-330);
  }

}
```

---

# 별을 주변에 뿌려보자

*역시나 create에서 진행해야한다.*

- 별들을 화면에 뿌리고 플레이어가 이 별들을 수집하게 해야 한다.
- 먼저 우리는 stars라는 새로운 그룹을 만들고, 반복문을 돌린다.
- `this.physics.add.group()`을 사용하여 개체를 그룹에 추가할 수 있다.
    - key: 이미지 키값
    - repeat: 반복횟수
    - setXY: 그룹이 화면에 생성될 때, 그 그룹의 자식들의 위치를 설정할 때 사용한다.
        - 만약 x를 12로 설정하고, stepX를 70으로 주면 12, 12+70, 12+70+70, ... 으로 위치하게 된다.

```jsx
//별 추가하기
  stars = this.physics.add.group({
    key: 'star',
    repeat: 11,
    setXY: {x: 12, y: 0, stepX: 70}
  });
  ...
```

- `그룹.children.iterate`를 사용하여 각각의 자식에 대한 설정을 할 수 있다.
- 아래 코드에서는 각각 child의 `setBounceY`를 설정했다.

```jsx
//별 추가하기
  ...
  stars.children.iterate(function(child) {
    child.setBounceY(Phaser.Math.FloatBetween(0.4, 0.8));
  })
```

- 추가한 별그룹이 `platforms`에 충돌하여 안착하기를 위하므로 `collider(별그룹, 충돌대상)`을 실행한다.

```jsx
...
this.physics.add.collider(stars, platforms);
```

- `this.physics.add.overlap`은 특정 대상들끼리 겹치는지 여부를 설정한다.
- `overlap(대상1, 대상2, 겹칠시 동작, null, this);`

```jsx
...
this.physics.add.overlap(player, stars, collectStar, null, this);
```

- `collectStar`는 플레이어와 별이 겹칠 때 별이 사라지도록 한다.(`disableBody`)

```jsx
function collectStar (player, star)
  {
    star.disableBody(true, true);
  }
```

![스크린샷 2021-09-04 오후 11.39.57.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3ab6f73b-2a9d-47b0-b44e-0801ef2a7f21/스크린샷_2021-09-04_오후_11.39.57.png)

---

# 스코어 설정을 하자!

- 스코어 설정을 위한 글자 배치는 `create`에서 하고, 점수 합산의 경우 `collectStar`에서 진행한다.
- 점수용 변수, 점수 글자용 변수를 전역으로 선언한다.

```jsx
let score = 0;
let scoreText;
```

- create에서 글자 크기, 문구를 설정한다.
- `this.add.text(x, y, '문구', { 글자 크기 등 css 속성 설정 })`

```jsx
function create() {
  scoreText = this.add.text(
    16,
    16,
    'score: 0',
    {
      fontSize: '32px',
      fill: '#000',
    }
  )
}
```

- `collectStar`에서 점수 합산을 하고 `setText`를 사용해 글자를 수정해준다.

```jsx
function collectStar(player, star) {
  star.disableBody(true, true);
  score += 10;
  scoreText.setText('Score: ' + score);
}
```

![스크린샷 2021-09-04 오후 11.54.29.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6632d1b5-59b7-4b84-ac00-d66961b9716c/스크린샷_2021-09-04_오후_11.54.29.png)
