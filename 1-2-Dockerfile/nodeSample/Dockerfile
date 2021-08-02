# ベースイメージを指定
FROM node
# 環境変数を設定
ENV NODE_ENV=production
# Dockerfile 内のコマンドの実行ディレクトリを指定する
WORKDIR /scripts
# ホストファイルをワークディレクトリにコピー
COPY . .
# あれこれ実行
RUN npm install \
  && groupadd app \
  && useradd -g app app \
  && chown -R app:app /scripts
# ログインユーザを指定
USER app
# 実行
CMD [ "npm", "start" ]
