# PTT Chat Generator

🎉 News: 我們採用了類似的資料採集策略訓練了一個繁體中文的大型語言模型，模型效能與使用方式可以參考 [Kyara](https://github.com/zake7749/Kyara)

本專案的目的在實作一個推文產生器，由使用者輸入一個隨機標題，即能回覆一個恰當的推文，目前仍在進行中 d(`･∀･)b

## 目前成果
```
MianBot: 您好，我是你的老朋友眠寶，讓我們來聊聊八卦吧 o_o

User: 為什麼李嚴要炸三隻蝦
MianBot: 一隻給小當家、一隻給及弟，另一隻給誰我不能說

User: 蘇美守得住街亭嗎
MianBot: 目前的戰力來看別說守街亭 都可以直接滅了曹魏

User: 起床第一件事要做什麼？
MianBot: 張開眼睛 給你參考

User: 初音只是個軟體嗎
MianBot: 初音是軟體 但是初音不只是個軟體

User: 有沒有鳳梨的八卦
MianBot: 聽說把鳳梨跟筆合在一起，就會變鳳梨筆

User: 肥宅要怎樣才有加分
MianBot: 減肥 先從脫離肥宅開始

User: 熊貓麻婆是怎麼贏豆腐三重奏的
MianBot: 小當家表示：張大人你好，我媽阿貝師傅，我爸李提督

User: obov現在還好嗎
MianBot: 你問的是前面還是後面？

User: 如果時間能重來，你想做什麼
MianBot: 珍惜身邊的人
```
## 類別說明

* [PTT-Crawler](https://github.com/zake7749/PTT-Crawler): 用於爬取 PTT 頁面的原始文章
* `filter.py`: 用於過濾 PTT-Crawler 爬取下來的文章，如濾除某些標籤或某些使用者的文章，或是濾除內容重覆的文章等
* `article.py`: 儲存 PTT 文章的結構，會將分段式的回覆合併起來
* `corpus.py`: 保存 Article 的結構，可遍歷文章的標題、內容
* `match.py`: 調用不同的 Matcher 實驗
* `Matcher`: 用於文本相似度的比對，將使用者的輸入與 Corpus 裡所有標題比較，回傳最相似的標題與索引
  * `FuzzyMatcher`: 基於 Levenshtein Distance 比對短語相似度
  * `VectorMatcher`: 基於 sentence2vec 比對短語相似度 TODO!
  * `KeywordMatcher`: 基於 tf/idf 比對短語相似度
  * `bm25Matcher`: 基於 Okapi BM25 (基於 snownlp)，目前默認採用該算法
* `ResponsesEvaluate`: 從推文中挑選出最佳推文
  * `Evaluator`: 基於推文的詞頻來選取最佳回應
  * `ClusteringEvaluator`: 基於聚類來選取最佳回應 TODO !
  
## 套件需求

* jieba : 中文隱性馬可夫模型斷詞器
* gensim : 使用詞袋、tfidf、word2vec
* fuzzywuzzy : 模糊字串比對
  * python-Levenshtein : 用於優化 fuzzywuzzy 計算速度的套件包
* sentence2vec (非必要)

## Data 說明

* raw: 儲存PTT-Crawler爬取的原始資料
* processed: 儲存已經篩選過的文章（如濾除特定標籤、使用者）
  * reply: 儲存文章回應，每 1000 筆為一個檔案（目前未上傳至 Github）
* processed_seged: 儲存文章或回應的斷詞結果
* stopwords: 儲存常用中文停用詞、PTT 停用詞、負面標籤
* User_info: 基於 raw 的使用者推噓文紀錄
* Titles.txt: 存放篩選後的文章標題
* SegTitles.txt: 存放已完成斷詞的篩選後文章標題

## 實驗簡述

1. 使用 PTT-Crawler 爬取文章後放置於 "data/raw/"
2. 使用 filter.py 從原始資料裡挑選出需要的資料
 - `ArticleFilter.load_processed_corpus()`: 用於載入先前處理好的語料(會載入 data/processed/ 中所有檔案)
 - `ArticleFilter.process_raw_data()`: 對 "data/raw/" 中的原始資料進行處理(如去除特定tag，過濾敏感回應等) 
 - `ArticleFilter.print_titles()`: 輸出處理好的文章標題
 - `ArticleFilter.print_response()` : 輸出處理好的文章回應
 - 為維持文章結構的一致性，使用 `print_titles()` 建議也呼叫 `print_response()`
3. 使用 match.py 測試匹配結果
 - 因為斷詞文件 (SegTitles.txt) 有些 `matcher` 不會用到，所以更新 Corpus 後請記得刪除已斷詞文件並重新斷詞
 
## 測試用資料集

* 請前往[這邊](https://drive.google.com/file/d/11JlbmYmuu00TfmfdAfyoGK_E3VMp8Vd_/view)下載，解壓縮後放置於 `data` 資料夾下，
`reply.rar` 解壓縮後的回覆請放置於 `data/processed` 中。

* 如果只想特別使用抽取完畢的問答配對，可以前往 [PTT 中文語料](https://github.com/zake7749/Gossiping-Chinese-Corpus) 了解進一步的資訊 :>
