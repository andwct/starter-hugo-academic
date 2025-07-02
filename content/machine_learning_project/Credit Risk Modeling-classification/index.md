---
title: "Unlocking the Secrets behind Machine Learning Models in Financial Credit Risk Modeling (1) — Binary Classification"
summary: This is summary
tags:
- Machine Learning Bias
- AI Fairness
date: "2021-08-02T05:27:20+08:00"

# Optional external URL for project (replaces project detail page).
# external_link: ""

# image:
#   caption: Photo by rawpixel on Unsplash
#   focal_point: Smart
toc: true
# links:
# - icon: twitter
#   icon_pack: fab
#   name: Follow
#   url: https://twitter.com/georgecushen
# url_code: ""
# url_pdf: ""
# url_slides: ""
# url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
image:
  placement: 1
#   caption: "Photo by [Geo](https://github.com/gcushen/)" 
  focal_point: "Center"
  preview_only: false
  alt_text: An optional description of the image for screen readers.
---


{{< toc >}}
<!-- [toc] -->
<!-- {{< include-md "content/machine_learning_project/AI_Fairness/AI_Fairness.md" >}} -->




--------
 Dataset: Home Credit Default Risk
 
[https://www.kaggle.com/c/home-credit-default-risk](https://www.kaggle.com/c/home-credit-default-risk)

--------

## 1.  Frame the Problem and Look at the Big Picture 


        我們目標在於使用監督式學習的模型，根據貸款者得申請資料及歷史資料，來協助我們判斷他在銀行放貸後是否有可能會違約。由於在大部分的情況銀行都是會放行貸款，且真正的違約的案例相比沒有違約的案例還少非常多，因此此問題為 Imbalanced Binary Classification 的問題。有鑑於資料的不平衡，在分類的效果評估上就不能單純仰賴 Precision 及 Recall，因為若我們直接把 test 的 data 全部 label 成不會違約，此兩個值就已經非常高。因此我們主要會使用 ROC_AUC 及 F1 去訓練一個效果不錯但在預測上相對不會太偏差的模型。
此資料集在 kaggle 前幾名的 ROC AUC 大致為 0.8，我們會以此來評估我們效能表現。由於我們缺乏信貸相關的 domain knowledge，因此沒有辦法以使用者的角度來看更高層次的問題，也無法以專家的角度評估分類器是否有用，我們只能說分析的workflow 似乎可以套用於其他會碰到 imbalanced data 的專案中，這個專案也讓我們了解到，僅了解數據分析的工具是遠遠不夠的，必須要與一個有 domain knowledge 的人協作才能有更多的 insight 。 
&nbsp;

## 2. Analyzing Fairness of a Machine Learning Task


        要衡量機器學習的公平性，要先了解下面幾個構面，定義出我們想要探討的議題，我們才知道要如何使用 aif360 的工具。

#### (1) Individual Fairness vs. Group Fairness

         Individual fairness 是在追求相近的個體，能夠被機器學習模型公平的對待；而 Group fainess 強調的是透過我們自己選擇的 protected attributes 將人分成多個 groups，期望機器學習模型可以公平的看待這些不同的 groups。我們想要探討的是這個 Home Credit Default Risk 資料集中，模型是不是對一個群體比另外一個群體有利，例如:男性與女性兩個群體，模型是公平的看待他們嗎?還是會對其中一個群體比較有利因而比較傾向於借貸給這個群體呢?因此我們會著重在 Group Fariness 的討論。

#### (2) Data vs. Model

        aif360的設計上可以讓 Fairness 在機器學習任務過程的三個階段被衡量，pre-processing（直接在training data上衡量）、in-processing (在機器學習模型上衡量)和 post-processing (對機器學習模型的output進行衡量)。分成三個階段是有原因的，主要是我們在執行機器學習任務時，不一定會有權限可以可以更動 training data，有時候我們只能套用不同的模型，或是對模型的輸出做處理。而aif360 tool kit 在三個階段中提供了不同的 mitigation algorithm，aif360 documentation 中有解釋到，如果我們的任務有最大的權限可以直接更動training data，那麼就可以直接使用 pre-processing 階段中的 evaluation metrics 還有消除 bias 的演算法。依照 documentation 的建議，我們會著重在 pre-processing 的階段探索 fainess。

#### (3) Group Fairness: We’re All Equal vs. What You See Is What You Get

        探索Group Fairness有兩大陣營：We're All Equal(WAE) 和 What You See Is What You Get(WYSIWYG)，兩陣營各自對 Group Fairness 有完全不同的定義。WAE 認為，在一個機器學習任務中，依照 protected attributes 區分的各個 groups 能力都是一樣的;但是 WYSIWYG 認為機器學習任務中，可以透過觀察或衡量的機制去區分每個 group 的能力。 舉一個例子來說，美國大學入學體制採用 SAT 分數作為衡量學生在大學階段是否會成功的關鍵因素。WYSIWYG 會覺得這是一個很好的作法，因為可以透過SAT考試去區分或比較每個學生的學習能力。但是從 WAE 的角度來看，因為他認為每個人的學習能力都是一樣的，所以 SAT 分數的高低不能反應出個人的學習能力的強弱。

        為甚麼要討論這兩個面向呢? 因為 aif360 有提供衡量兩個面向的 evaluation metrics ，要怎麼使用這個 metrics，是依照我們想要探討的面向所決定的，而我們會使用到兩個面相做討論。後面內容會明確的解釋兩個 metrics。


![image info](wusiwug.png)

#### (4) Group Fairness: Ratios vs. Differences

     AIF360 toolkit 提供了 Ratios 和 difference 的 evaluation metrics 。但兩者所表達的意義是一樣的。只是一個是用 ratio 所呈現，一個是由兩個群體的 differences 所呈現。

&nbsp;

## 3. Introducing Evaluation Metrics

如同前面所說，用來衡量 AI fairness 的指標很多，而每個指標所衡量的 bias 的意義不盡相同，以下僅選用 AIF360 中所使用的四種指標做為參考

&nbsp;
#### (1) Statistical Parity Difference

$$p\_1: \\frac{the\\, number \\, of \\, favorable\\, outcome}{the\\, length\\, of\\, target\\, value}\\, in\\, priviliged\\, group$$$$p\_2:\\frac{the\\, number \\, of \\, favorable\\, outcome}{the\\, length\\, of\\, target\\, value}\\, in\\, unpriviliged\\, group$$$$Statistical\\, Parity\\, Difference=p\_2-p\_1 $$

分別可對 input dataset 或是 predicted dataset 計算其 SPD，若是 SPD=0 表示兩群體的利益相等，若是 SPD<0 表示該資料結構對 privileged group 比較有利，反之若是 SPD>0 則對 unprivileged group 比較有利


#### (2) Disparate Impact

$$p\_1,\\, p\_2 \\, are\\, identical\\, to\\ those\\, in\\,Statistical\\, Parity\\, Difference $$$$Disparate Impact= \\frac{p\_2}{p\_1} $$

同樣地在 input dataset 或是 predicted dataset 都可計算其 Disparate Impact，若是 DI=1 表示兩群體的利益相等，若是 DI<1 表示該資料結構傾向偏袒 privileged group，反之若是 DI>1 則傾向維護 unprivileged group

#### (3) Average Odds Differnece

$TPR\_i=true\\, positive\\, rate,\\, FPR\_i=false\\, positive\\, rate\\,$

$i =1,2\\,$

$1\\, denotes\\, privileged\\, group;\\,$
$2\\, denotes\\, unprivileged\\, group\\,$
$$ Average\\, odds\\, differnece(AOD)= $$$$\\frac{(FPR\_2-FPR\_1)+(TPR\_2-TPR\_1)}{2}$$

計算Average\\, odds\\, differnece 需要 input dataset 以及 predicted dataset。若是 AOD=0 表示兩群體的利益相等，若是 AOD<0 表示該資料結構對 privileged group 比較有利，相反地若是 AOD>0 則對 unprivileged group 比較有利。

#### (4) Equal Opportunity Difference

$$Equal\\, opportunity\\, difference= TPR\_2-TPR\_1 $$
     

同樣地計算 Equal opportunity difference 時需要 input 以及 predicted 兩個資料集。若是 EOD=0 表示兩群體的利益相等，若是 EOD<0 顯示該資料結構有利於 privileged group，若是 EOD>0 則對 unprivileged group 比較有利。其中 Statistical Parity Difference 與 Disparate Impact 兩線指標是由 We’re All Equal 的價值觀出發所設計，且兩個指標的就衡量 bias 的意義上十分接近，認為不論是哪一群體，其預測出favorable outcome的比例應該要相同。而 Average odds differnece 與 Equal opportunity difference 則是基於 What You See Is What You Get 的理念所設計，認為在群體之內，其預測出 favorable outcome 的比例要相同，因此用 TPR, FPR衡量 bias 大小。

&nbsp;

## 4.  Introducing our Protected Attributes:


![image info](protectedattribuates.png)

- 性別
- 年齡
- 學歷



        銀行在貸款審核的過程會參考幾項重要的核貸條件，像是:無擔保貸款餘額是否超過月收的22倍、無法提供收入證明，或是貸款過去12個月曾遲繳...等等，這些指標確實可以幫助銀行合理且客觀地評判申請者的還款能力，以及違約的風險。然而我們在探索資料的過程中，發現年齡、性別與教育程度這3項個人基本資料的變數，在 feacture impotance 中都排名在前10名以內，表示在機器學習的過程中，年齡、性別與教育程度對於模型的預測能力影響甚多，不過客觀而言這3個變數與還款能力並沒有絕對相關，若是根據這3變數衡量客戶的違約風險，恐怕有失公允，因此我們選擇年齡、性別與教育程度作為 protected attributes，分別觀察在不同 protected attributes 中是否確實有 bias 存在，以及如何消弭 bias 的效應。  

![image info](2.png)


在這裡，我們定義不同面向中的 privileged, unprivileged groups:



{{< table id="sample" class="bordered" data-sample=10 >}}
|     | 性別    | 年齡    | 教育程度 |
|---| :---        |    :----:   |          ---: |
|privileged|女|	平均以上|	大專以上	|
|unprivileged|	男|	平均以下|	大專以下|
{{</table>}}


&nbsp;

## 5. Bias in Data 


|     | 性別    | 年齡    | 教育程度 |
|-----|---------|---------|----------|
|  DI |  0.9699 |  0.9609 |  0.9467  |
| SPD | -0.0276 | -0.0362 | -0.0503  |
| SPD | -0.0276 | -0.0362 | -0.0503  |
| SPD | -0.0276 | -0.0362 | -0.0503  |


![image info](1.png)
&nbsp;
        Disparate Impact 以及 Statistical Parity Difference 兩種指標可以用來衡量不論是 training dataset 或是 predicted output 中的 bias ，而 DI 若是<1，或是 SPD<0 表示該資料結構不利於 unprivileged groups。在我們訓練資料中觀察到不論是性別、年齡、或是教育程度三個面向中 DI 都小於1，SPD 都<0，且經過檢定都有統計顯著，表示在這筆訓練資料中男性沒有違約的比例顯著低於女性、年輕族群沒有違約的比例顯著低於年紀較長的族群、學歷在高中以下的人沒有違約的比例也顯著低於學歷在大專以上的人。而這樣的資料結構可能會讓該筆資料訓練出來的模型帶有相同的偏差，因此銀行會認為女性、年紀較長、或是教育程度高的客戶違約風險較低，而比較願意借貸給這些人，相反地，男性、年輕、或是教育程度較低的客戶則比較不容易申請到貸款。

&nbsp;

## 5. Comparison of Bias in Logistic Regression and Light-GBM

![image info](sex.png)
![image info](age.png)
![image info](education.png)

        我們分別使用兩種模型 Logistic regression 與 Light-GBM 欲比較不同的演算法對於 bias 是否會有影響，因此這邊透過這兩個模型預測結果分別計算: SPD, DI, AOD, EDO 這四種衡量偏差大小的指標， 由於樣本大小很大使得估計值變方接近為0，因此不論是性別、年齡、或是教育程度哪一面向，兩個模型的所產生的偏差都十分顯著，然而在訓練資料中就已經看見不同的群體中:男性 v.s. 女性、年輕較輕 v.s. 年紀較長、大學以下 v.s. 大學以上，確實有偏差存在，因此使用這個本身就帶有偏差的訓練資料集，很可能會使得訓練出來的模型沿襲抽樣資料中固有的偏差。  
        不過從上方三張圖可以明顯地看出，不論在性別、年齡或是教育程度哪一方面，Light-GBM 模型中四種指標的 bias 都比 Logistic regression 來的更加嚴重，可見不僅僅抽樣資料本身的結構會影響機器學習的公正性，不同的演算法影響偏差的效應也不盡相同，在我們的例子中顯然 Light-GBM 模型會放大資料中的偏差。

&nbsp;

## 6. Mitigation Algorithms in Practice


#### (1) Which mitigation algorithm is prefered?

![image info](2.png)

         AIF360 提供了減少機器學習 bias 的方法，這些方法根據機器學習的三個階段區分類，依序可以分為:適用在 training data 上的 pre-processing 演算法；適用在機器學習模型的 in-processing 演算法;以及適用在模型輸出結果後的 post-processing 演算法。  
         我們參考介紹 AIF360 的論文中，對於各個方法的評估與建議，in-processing 的方法準確度都太差；而 post-processing 的方法，其中兩個 equal odds post-processing 對於減少偏差沒什麼效果，而第三種雖然可以有效減少 bias，不過同時也會大大犧牲模型預測的準確度，因此論文中建議若是操作者可以操作訓練資料，建議使用 pre-processing 中的 Reweighing method ， Reweighing 可以有效降低偏差，也不會對模型預測能力造成太大的影響，因此我們在這次的報告中採用 AIF360 設計者所推薦的 Reweighing method 來削減 bias。


![image info](mitigation.png)

Fairness vs. Balanced Accuracy before (top panel) and after (bottom panel) applying various bias mitigation algorithms. Four different fairness metrics are shown. In most cases two classifiers (Logistic regression - LR or Random forest classifier - RF) were used. The ideal fair value of disparate impact is 1, whereas for all other metrics it is 0. The circles indicate the mean value and bars indicate the extent of ±1 standard deviation. Dataset: Adult, Protected attribute: race.

(Bellamy et al.,2018 :[https://arxiv.org/pdf/1810.01943.pdf](https://arxiv.org/pdf/1810.01943.pdf))



#### (2). Comparison of Model Quality and Bias after Mitigation

![image info](figure3.png)
![image info](modelcaomparison.png)

        根據 Figure 3.發現在 Reweighing 之後，模型預測能力的 precision 下降了，不過 recall 反而往上提升了，表示Reweighing 之後，使得 False negative rate 會減少，而 False positive rate 增加，且整體而言 False positive rate 增加的速度是大過 False negative rate 減少的速度的，因為整體 accuracy 是下修的。在原本的資料中存在嚴重的 imbalance，所以訓練出來的模型會傾向預測 negative，所以雖然 accuracy 達到 86.9%，不過大都都是由 True negative 所貢獻， True positive 的比例很少，從我們的例子中發現在經過 Bias mitigation 之後，會修正資料中 imbalance 的效應，使整個模型的預測結果，向 positive 靠攏。雖然 accuracy 減少了14.6%，不過ROC值並沒有減少多少，而由於資料 imbalance 太嚴重，因此參考 accuracy 並不準確，選擇 ROC 作為衡量模型的指標比較合適，表示透過 Reweighing 減少偏差的同時， 不需要犧牲模型的預測能力，依然可以有不錯的表現。


![image info](cool1.png)
![image info](cool2.png)
![image info](cool3.png)

從上方三個圖可見訓練資料在經過 Reweighing 之後，不論在性別、年齡或是教育程度三個面向而言，AOD, DI ,EOD, SPD 四種指標bias都明顯地下降，而這四種指標分別是基於 We’re All Equal 跟 What You See Is What You Get 兩種公平性價值觀所設計，可見透過 Reweighing 來減少bias可以同時滿足基於 WAE 或 WYSWYG 兩種價值觀所認定的公平性。



## 7. Conclusion

        我們首先發現機器學習演算法中過度解釋某些個人基本信息，尤其是性別、年齡以及教育程度這三個變數，其 feature impotance 都在前 30名，表示對於機器學習演算法倚賴這些變數作為預測時的重要依據，然而這三個變數跟該顧客的還款能力並沒有直接相關，因此若是過分解釋這些變數，可能會讓銀行判斷顧客的違約風險時有所偏頗，進而剝奪了特定族群的權益。由此我們回過頭去檢視訓練資料的結構中對於不同性別、年齡或是教育程度的族群，其正常履行合約的比例確實有顯著差異:女性顯著高於男性；年紀較長的族群顯著高於年輕族群；大專學歷以上的人顯著高於高中學歷以下的人。因此用該筆訓練資料所建立的模型就很有可能會沿襲這樣的偏差，例如傾向認為女性比起男性的違約風險更低，因此女性會比起男性更容易申請到貸款。為此我們接著檢視原先模型的預測結果是否真的受到訓練資料偏差的影響，同時比較不同的預測模型會不會影響 bias 大小，在這裡我們比較了 Logistic regression 跟 Light-GBM 兩種模型，發現兩個模型都確實承襲了訓練資料中的偏差，另外也發現 Light-GBM 比起 Logistic regression 不管是在三種面向，或是不同的 bias metrics 中偏差都更嚴重，表示不僅抽樣資料的結構會造成不公正的預測結果，選擇不同的演算法有可能會進而放大資料中的偏差。

        在發現我們的模型的預測結果對於不同族群的準確度有顯著不同時，我們參考 AIF360 所建議的削減偏差的演算法，希望在降低偏差的同時，不會犧牲太多模型的預測能力。根據 AIF360 對於不同演算法的評估，認為在有權取得訓練資料的狀況下，事先對訊資料做 Reweighing 的前處裡，可以最有效地降低偏差，同時不會影響預測能力。因此針對我們預測能力最高的模型 Light-GBM 進行 Reweighing 之後，比較處理前後模型的預測能力，發現 ROC 確實沒有太大的影響，不過 Percision 跟 Accuracy 都有明顯下降，然而 Recall 反之上升，表示 False positive rate 增加的速率比起 False negative rate 減少的速率更大，反映出 Reweighing 之後模型預測客戶違約的比例增加，稍微修正了訓練資料中 Imbalance 的效應，雖然會因此降低 Accuracy ，但這樣相對保守的預測結果對銀行而言或許不完全是會壞事，因為銀行在拒絕優良顧客的同時，也減少了貸款給違約風險高的族群的風險，減少追不回貸款的狀況。而對於不同性別、年齡以及教育程度的群體而言，經過 Reweighing 之後模型的預測結果也確實可以讓預測結果針對:女性 v.s. 男性、長輩 v.s. 年輕人、大專以上 v.s. 高中以下 不同群體的人得到同樣公正的預測結果，而且不管是哪一種衡量偏差的指標， Reweighing 都確實有效，而這些指標是根據對於公正性不同的認知所定義的，而透過這個方法修正之後的模型也可以符合對於多元的公平性的期望。 

        這份報告中我們主要是參考 IBM 所提供的 AIF360 其中主要用來衡量偏差大小的指標，以及其整理多篇文章所提供的降低 bias 的演算法，不過 IBM 也提醒使用者目前對於 AI Fairness 的定義還沒有共識，因此也沒有一個通用的量化 bias 的指標，自然 AIF360 無法涵蓋所有 bias metrics 或是 mitigation algorithm ，這些方法也不一定適用於所有社會正義的機器學習任務上，不過 AIF360 確實提供了一個使用友善的工具，讓關心機器學習公正性的人們可以花較少的學習成本來探索這個議題。