# javascript チートシート

## 定義している部分

1. CSS スタイル
   css のクラス名とその内容を決めています。
   CSS スタイルを決めるのは"style"タグで囲まれた中に書く。  
   class="push"が書かれている部分は「背景色が赤色」に。  
   class="blue"が書かれている部分は「背景色が青色」に。

2. 入力フォームのＩＤ

```
<style>
  .push {
    background-color: red;
  }
  .blue {
    background-color: blue;
  }
</style>

<input id="InputA" class="Main classA" />
<input id="InputB" class="Main classB" />
<input id="InputC" class="Sub classC" />

<button onclick="ClickEvent_Id()">getElementById</button>
<button onclick="ClickEvent_Class()">getElementByClass</button>
<button onclick="ClickEvent_Tag()">getElementByTag</button>
<button onclick="ClickEvent_If(5)">If</button>


<div id="inner"></div>

```

## 定義している部分

いよいよ動きが表現される部分です。
"script"タグで囲んだ中に書いていきます。

```
<script>
  ClickEvent_Id = () => {
    var ElementInputA = document.getElementById("InputA");
    var ElementInner = document.getElementById("inner");
    ElementInner.innerHTML = ElementInputA.value;
  };

  ClickEvent_Class = () => {
    var ElementMain = document.getElementsByClassName("Main");
    console.log("MainClassの数は:" + ElementMain.length);
    for (let i = 0; i < ElementMain.length; i++) {
      ElementMain[i].classList.add("push");
    }
  };

  ClickEvent_Tag = () => {
    var ElementInput = document.getElementsByTagName("input");
    window.alert("Inputタグの数は:" + ElementInput.length);
    for (let i = 0; i < ElementInput.length; i++) {
        ElementInput[i].classList.add("blue");
    }
  };

  ClickEvent_If = (item) => {
    if(item == 5){
        window.alert('あっています');
    }
  };

</script>

```
