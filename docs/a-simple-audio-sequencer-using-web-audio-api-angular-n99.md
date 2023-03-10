# 一个简单的使用网络音频 Api 和 Angular 的音频序列器

> 原文：<https://dev.to/elasticrash/a-simple-audio-sequencer-using-web-audio-api-angular-n99>

出于各种个人原因，我决定将我的帖子从 medium 移到这里。当我移动它们的时候，我也会把它们从那里删除，没有理由两个都保留。我从最不受欢迎的开始。:D

和大多数开发人员一样，我把空闲时间花在开发上……或者游戏上(咳咳)，或者自从我成为父母之后……更多的是寻找睡觉的机会，而不是开发。

然而，这次我决定创建一个简单的最小音频序列。

因此，让我们假设我实际上给了这个项目一点思考，并在我开始开发之前写下了我的需求。

*   想象代表不同声音的 n 个元素的序列
*   这些元素可以打开和关闭(静音/改变颜色)
*   将执行序列的开始按钮
*   执行每个元素时改变颜色
*   完成后，重置每个元素的状态和颜色

为什么有棱角？为什么不呢？只是个人喜好。

我的应用程序有一个组件和一个服务。

```
> sequencer.component.ts
> sound.service.ts 
```

我的型号如下

```
interface Block {
    color: string; // hex color
    state: true; // true = sound, false  = no sound
    note: Note;
} 
```

```
interface Note {
    name: string; // name of the note
    frequency: number; // frequency (hertz) of the note
    position: number; // I don't use it but its useful to know
                     // the octave this frequency corresponds to
} 
```

…现在困难的工作已经完成，我们可以开始实施想法的简单部分了。

首先是我们的服务。

```
@Injectable()
export class SoundService {
  // initially i had like 6 octaves but it was pretty pointless
  // so I trimmed it down to 2 (octave 4 and 5)
  public notes = [
  {
    name: 'C',
    position: 4,
    frequency: 261.63
  }, {
    name: 'C#',
    position: 4,
    frequency: 277.18
  }, {
    name: 'D',
    position: 4,
    frequency: 293.66
  } ... ] // I am not going to list all the notes
  private audioCtx = new (window['AudioContext'] || window['webkitAudioContext'])();
  private gainNode = this.audioCtx.createGain();
  public play(freq, time, delay) {
    const oscillator = this.audioCtx.createOscillator();
    oscillator.connect(this.gainNode);
    this.gainNode.connect(this.audioCtx.destination);
    oscillator.type = 'sine'; 
    oscillator.frequency.value = freq;
    oscillator.start(this.audioCtx.currentTime + delay);
    oscillator.stop(this.audioCtx.currentTime + delay + time);
  }
} 
```

没什么特别的，我们定义了以下变量。

*   一张便条🎶具有相应频率的对象
*   音频环境🎹其处理信号
*   控制音量的增益节点🔊

最后是我们的游戏功能。我使用播放功能的方式有点奇怪。当我想让注释被执行时，我不是发送一个注释，而是延迟发送一个注释，这样我就可以发送整个序列。这样做的缺点是序列不能被停止(当然，如果你有来自振荡器对象的引用除外)。

我们的模板(sequencer.component.html)真的很简单…简单到有点傻🔥

```
<div class="pad">
  <div *ngFor="let block of blocks;let i = index" class="block">
    <div class="single-block"
         [ngStyle]="{'background-color': block.color}"
         (click)="changeState(i);">
      {{ block.note.name }}
    </div>
  </div>
</div>
<button class="btn" (click)="play()">start</button> 
```

现在我们的主要部分是 sequencer.component.ts！

我们定义一些变量

```
@Component({
  selector: 'sequencer-pad',
  templateUrl: './sequencer.component.html',
  styleUrls: ['./sequencer.component.css']
})
export class PadComponent implements OnInit {
  public blocks: Block[] = [];
  private blockSize = 13; // sequencer will use 13 notes
  private noteLength = 1; // duration of the note (1 second)
  constructor(private soundService: SoundService) { }
  ...
} 
```

*   积木是我们的建筑材料😃
*   blockSize 是我们在服务中定义的希望在序列中使用的音符的数量。我这样做是为了让自己听起来不那么无聊。如果我想创建一个更真实的音序器，我可能会有一个块数组的集合，每个数组都有一个独特的声音。
*   注意长度，这基本上是声音的持续时间🎵出品。在这种情况下，1 秒钟应该没问题。
*   在 ngOnInit()上，我创建了我的块数组

```
ngOnInit() {
  // add default values to the blocks array
  for (let index = 0; index < this.blockSize; index++) {
    this.blocks.push({
      color: 'limegreen',
      state: true,
      note: this.soundService.notes[index]
    });
  }
} 
```

*   当你点击一个便笺时，你需要改变它的颜色和状态

```
/**
 * change the color of the div, and switch its state (on/off)
 * @param index
 */
public changeState(index: number) {
  this.blocks[index] = (this.blocks[index].color === 'limegreen') ?
  {
    color: 'tomato',
    state: false,
    note: this.blocks[index].note
  } : {
    color: 'limegreen',
    state: true,
    note: this.blocks[index].note
  };
} 
```

*   当序列结束时，我们需要重置颜色和状态

```
/**
 * when sequence ends this returns the colors but to limegreen
 */
private resetColor() {
  this.blocks.forEach(element => {
    element.color = 'limegreen';
    element.state = true;
    });
  }
} 
```

*   播放序列，并适当地涂上颜色

```
/**
 * play the notes that have a true state
 */
public play() {
  this.blocks.forEach((element, index) => {
    if (element.state) {
      const note = this.soundService.notes[index];
      this.soundService.play(note.frequency,
      this.noteLength, index * this.noteLength);
    }
    // this is to emulate the progress
    setTimeout(() => {
      element.color = 'lightpink';
      if (index + 1 === this.blocks.length) {
        setTimeout(() => {
          this.resetColor();
        }, this.noteLength * 1000);
      }
    }, this.noteLength * 1000 * index);
  });
} 
```

就像我之前说的，我正在迭代块数组，并以适当的延迟将每个音符发送到 soundService。因为我设计它的方式，当一个音调开始和结束时，我没有来自 soundService 的反馈，所以我必须使用 setTimeout(两次)来模拟 UI 中序列的进度。

额外收获:我使用的风格

```
.pad {
margin-top: 20px;
display: flex;
flex-direction: row;
}
.single-block {
flex: auto;
min-height: 40px;
min-width: 40px;
display: inline-block;
border: none;
margin-right: 5px;
cursor: pointer;
border-radius: 5px;
text-align: center;
border-color: green;
border-style: solid;
}
.btn {
margin-top: 20px;
color: black;
background: #ffffff;
text-transform: uppercase;
padding: 20px;
border: 5px solid black;
border-radius: 6px;
display: inline-block;
}
.btn:hover {
color: #ffffff;
background: green;
transition: all 0.4s ease 0s;
} 
```