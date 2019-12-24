# Text Summarizer using BERT
## はじめに 
 このリポジトリはニューラルネットワークを用いた深層学習による自動文章要約システム（仮）のためのものです。
 モデルの学習や設定については、以下のリポジトリを参照してください。
 
 https://github.com/IwasakiYuuki/Bert-abstractive-text-summarization
 
 また、dockerイメージのベースにDocker Hubのpytorchイメージをそのまま使用しているためサイズが非常に大きくなっています。
 軽量化したい場合は、Alpineなどの軽量なOSをベースにPytorchのイメージを作成した上で、Dockerfileを編集してください。

## 使用方法
 GPUを使用する場合にはnvidia-dockerが必要です。
### コンテナの起動
```bash
git clone https://github.com/IwasakiYuuki/text-summarizer-using-BERT
cd text-summarizer-using-BERT
chmod +x install_model.sh 

# AWS S3から学習済みモデルをダウンロード＆展開
./install_model.sh 

# Dockerイメージを構築（デフォルトでCMDは"sh ./run_server.sh"）
docker build -t [任意のイメージ名] .

# Dockerコンテナを起動（CUDAを使用する際には、"--runtime=nvidia -e CUDA=1"を[上のイメージ名]の前に追加）
docker run -it --rm -d -v `pwd`:/var/www -p [任意のポート]:6006 [上のイメージ名]
```
 コンテナを起動したら、http://localhost:8000/summarize（ポートは任意）にPOSTでJSONを送信すると、要約された文章がJSON形式で受信できます。
 
### JSON送信例
```python
def summarize(source_text):
    url = "http://127.0.0.1:8000/summarize"
    method = "POST"
    headers = {"Content-Type" : "application/json"}

    # PythonオブジェクトをJSONに変換する
    obj = {"source_texts": [source_text]}
    json_data = json.dumps(obj).encode("utf-8")

    # httpリクエストを準備してPOST
    requests = urllib.request.Request(url, data=json_data, method=method, headers=headers)
    with urllib.request.urlopen(requests) as response:
        response_body = json.loads(response.read().decode("utf-8"))
        print(response_body['summaries'][0])
        return response_body['summaries'][0]
```


## 運用について
 上で作成されるコンテナは、Flaskのみを使用した簡易的な開発用サーバです。
 そのため、実際に運用する際にはWebサーバやWSGIを使用してデプロイしてください。
 この辺は全くの初心者なので適宜お願いします。