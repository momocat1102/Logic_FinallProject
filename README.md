# Logic-Design-Final-Project

---

## 第六組期末Project
### 109321052 資工二 林俊諺
### 109321053 資工二 繆亭霄

---
## 示範影片
* [操作影片連結](https://youtu.be/SH1grgiGaM4)
  ![image](https://github.com/CorgiCake/Logic-Design-Final-Project/blob/main/final.jpeg)


## 語言環境
* 語言：SystemVerilog
* 環境：Quartus II 13.1

---

## 使用的IO
* 8X8 RGB 顯示器：遊戲畫面
* 1 bit 按鈕 * 3：左右移動、發射激光
* 1 bit 指撥開關 * 1：發射強化激光
* 4 bit LED燈：血量條
* 1 bit LED燈：強化激光充能次數
* 1 bit 指撥開關 * 4：三種難度調整、暫停、Reset
* 1 bit 指撥開關 * 1：音樂選擇
* 七段顯示器：顯示分數
* 蜂鳴器：遊戲音樂

---

## 功能
*	最上方隨機位置掉落敵方飛機
*	我方飛機使用激光攻擊敵方飛機，打中即可加一分，分數達十分勝利
*	吃到補給品 (藍色球) 可以充能強化激光
*	激光有冷卻時間，與強化激光冷卻分開計算
*	被敵人碰撞後，血量扣一，血條歸零遊戲失敗
*	勝利後出現 “ V I C T O R Y ” 在 RGB 顯示器上
*	失敗後出現 “ D E F E A T ” 在 RGB 顯示器上
*	遊戲配樂為 “ 紅蓮的弓矢 ” & “ 妖精的尾巴 ”
*	遊戲難度影響飛機掉落速度，多種難度皆為開啟時，以較難者為優先
*	難度全關即為暫停
*	Reset 開啟，即可重新遊戲
