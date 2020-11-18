---
title: "﻿[Tip] 구글 코랩 자동 재연결 방법 (How to prevent Google colab from disconnecting?)"
layout: post
author: Jeonghyeok Park
categories: Natural-Language-Processing
tags: Natural-Language-Processing
---

﻿

﻿

안녕하세요.

요즘 논문 rebuttal 때문에 논문 실험 내용 보충을 위해 Colab으로 6개, 학교 연구실 서버로 2개 총 8개의 GPU를 사용해서 모델을 훈련하는 중이라 정신이 없어서.. 했던 모델 또 훈련하기도 한답니다.
이렇게 정신없는 와중에 google colab 연결이 자는 중에 꺼지기라고 하면 낭패죠.. 잠 잤는데 시간을 낭비한 느낌?

최근에 저도 답답해서 찾아보니 이미 다른 사람들도 전부 공감하고 있는 부분이더라구요. 그리고 약간의 트릭같은 해결법이 많이 있었습니다.
재연결 방법 이전에 제가 Googel colab에서 GPU를 사용하면서 체득(?)한 Tip들을 알려드리겠습니다.

저는 현재 2개의 구글 아이디로 colab pro를 모두 구매했습니다.
처음에는 꽁짜에 취해 그냥 써보려고 했지만, 여러개 GPU를 사용하다보면 바로 제한이 걸려서 한 epoch도 훈련을 못하는 경우도 있어 그냥 결제해버렸습니다. Pro 만원에 연동되는 구글 드라이브 2T 만원 해서 한 달에 2만원이면 좀 더 쾌적하고 스트레스 덜 받고 모델 훈련할 수 있습니다. 사용하다보니 2T로 사실 적은 것 같아 더 업그레이드할까 생각 중입니다.

일단 Google colab Pro의 이점은 여러개의 GPU를 비교적 긴 제한시간으로 사용할 수 있다는 점입니다.
그리고 또 하나 중요한 것은 고용량 메모리를 사용할 수 있습니다.

사실 더 좋은 GPU를 좀 더 긴 시간 동안 사용할 수 있다는 것이 가장 큰 매력이지만, 메모리 또한 중요합니다. data preprocessing을 할 때 메모리가 부족하면 여러가지 귀찮은 일이 많이 생기고, 모델 훈련 중에도 GPU RAM만 사용하는 것은 아니기 때문에 RAM은 용량이 클 수록 좋습니다. 다다익램이 괜히 생긴 말이 아니죠.
하지만 Google colab Pro도 무적은 아닙니다. 사실 한달에 만원에 GPU를 계속 쓸 수 있으면 굳이 직접 GPU를 사서 여름에 안 그래도 더운데 컴퓨터 열받게 할 필요가 없죠. Pro 역시 제한은 있습니다.

아래는 공식 구글 코랩 홈페이지의 내용입니다.

![]({{ site.baseurl }}/assets/disconnect1.png )

사실 좀 애매한 감이 있습니다. 딱 얼만큼 쓰면 제한한다는 것이 아니라 상황에 맞게 유동적으로 바뀌는 것 같아요.
한 반년 간 사용하다보니 어떻게 해야 제한이 걸리고 안 걸리는 지 대충 알 것 같아요.

제가 사용해본 결과 최대로 4개의 주피터 노트북으로 GPU을 사용할 수 있는 것 같아요. 그 이상부터는 메모장에 자원을 GPU로 설정할 수 없어요. 하지만 고용량 램은 무제한입니다.
4개의 메모장(주피터 노트북)을 키고 전부다 GPU를 사용하면 하루가 안되서 제한을 받습니다. 제한 기간 동안은 GPU를 이용할 수 없기 때문에 제한이 풀리려면 대략 하루 정도 기다려야 합니다.
3개 메모장은 거의 계속 돌릴 수 있는 것 같습니다. 지난 몇 달간 3개 메모장으로 계속 돌려도 문제없었습니다.

최근에 한 번 어떤 이유인지는 모르겠지만, 아마 공식 홈페이지에 나와있는 내용대로 제한 기준도 조정되는 것 같습니다. 2개 메모장을 돌려도 제한이 걸리더군요. 한 10시간 정도 돌리니 제한이 걸리고 한 10시간 있다가 풀렸습니다. 현재(2020.11.13)는 다행이 3개 메모장은 문제없이 계속 돌릴 수 있는 것 같습니다.

메모장에도 제한이 있습니다.

![]({{ site.baseurl }}/assets/disconnect2.png )

Pro의 경우, 24시간 동안 연결 유지가 가능하고, 무료 버전의 경우 12시간 연결 유지가 가능하다고 합니다.
하지만 실제 사용해본 결과 원인 모를 연결 끊김과 제가 중국에 있는 관계로 인터넷이 불안정하기 때문에 밤새 모델 훈련시켜 놓고 아침에 신나는 마음으로 모니터를 켜면 전부 disconnect.. 마음이 찢어져요..정말

인터넷이 끊긴다면 어쩔 수 없지만 원인 모를 연결 끊김를 방지하기 위한 몇가지 방법을 알려드리려 합니다.
그리고 덤으로 3개의 메모장을 크롬/파이어 폭스에서 훈련하다보면 로그가 쌓여 컴퓨터가 느려지고 훈련 상황을 확인할 수 없는 경우가 생기는 데 최고 좋은 방법은 모델 훈련 시에 log를 출력하지 않는 것이지만 log가 모델 훈련 상황의 모든 것을 보여주기 때문에 출력하지 않을 수는 없는 것 같습니다. 이를 위해 정기적으로 log를 삭제하는 방법도 알려드리겠습니다.

#### 연결 유지 방법

페이지에서 F12나 우클릭을 한 후 "검사"로 들어가주세요.
그리고 상단의 메뉴바중 Console을 클릭해주시면 아래와 같은 화면이 나옵니다.


![]({{ site.baseurl }}/assets/disconnect3.png )

이 콘솔창에 아래 명령어 중 하나를 복붙하셔서 엔터치시면 됩니다.
만약 연결이 잠깐 끊겼을 때 90분간 다시 연결이 없으면 연결이 완전 끊긴다고 공식홈페이지에는 나와있지만, 저는 체감상 90분보다 더 일찍 연결이 끊기는 것 같아서 30분마다 연결 확인을 하도록 설정했습니다.
(90분도 괜찮다 하시는 분은 아래 코드에서 30분을 원하시는 시간으로 바꾸시면 됩니다.)

```
function ClickConnect() {
var buttons = document.querySelectorAll("colab-dialog.yes-no-dialog paper-button#cancel"); 
buttons.forEach(function(btn) { btn.click(); }); 
console.log("reconnect every 30 minutes"); 
document.querySelector("colab-toolbar-button#connect").click(); 
} setInterval(ClickConnect,1000*60*30);
```

```
function ClickConnect(){
console.log(reconnect every 30 minutes""); 
document.querySelector("colab-toolbar-button#connect").click() 
} setInterval(ClickConnect,1000*60*30)
```

```
function CleanCurrentOutput(){ 
    var btn = document.querySelector(".output-icon.clear_outputs_enabled.output-icon-selected[title$='currently executing...'] iron-icon[command=clear-focused-or-selected-outputs]"); 
    if(btn) { console.log("clear log every 30 minutes");
     btn.click(); 
    } 
} setInterval(CleanCurrentOutput,1000*60*30);
```

#### 실행 중인 코드 log 삭제 방법

마찬가지로 아래 코드를 콘솔창에 입력하시고 엔터를 치시면 됩니다.
여러 메모장을 켜고 훈련을 하다보면 log가 쌓여 컴터가 느려지는 경우가 있습니다. 이를 방지하기 위해 아래 코드를 콘솔창에 쳐주세요.

```

function CleanCurrentOutput(){ 
    var btn = document.querySelector(".output-icon.clear_outputs_enabled.output-icon-selected[title$='currently executing...'] iron-icon[command=clear-focused-or-selected-outputs]"); 
    if(btn) { console.log("clear log every 30 minutes");
     btn.click(); 
    } 
} 
setInterval(CleanCurrentOutput,1000*60*30);

```

감사합니다.

오늘 포스팅은 여기까지입니다.

사진 설명을 입력하세요.

﻿출처: 

https://medium.com/@shivamrawat_756/how-to-prevent-google-colab-from-disconnecting-717b88a128c0

https://stackoverflow.com/questions/57113226/how-to-prevent-google-colab-from-disconnecting
﻿