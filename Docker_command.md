**Dockerコンテナをビルド**

```jsx
docker-compose build
```

**Dockerコンテナを起動**:

```jsx
docker-compose up
```

-d オプションを追加するとデタッチモード（バックグラウンドモード）でコンテナを実行できる

```jsx
docker-compose up -d
```

**コンテナの停止**:

```jsx
docker-compose down
```

データベースを作成

```jsx
docker compose run --rm web rails db:create
```

コンテナ内で**bundle install**

```jsx
docker-compose -f compose.yml run web bundle install
```

**Docker内部でRailsコンソールを実行する**

```jsx
docker-compose exec web rails c
```

**`web`** コンテナ内で**rails db:migrate**
```jsx
docker-compose exec web rails db:migrate
```
