# ECS Laravel Docker Environment

## 概要
Amazon ECS でLaravel アプリケーションを動作させるためのDocker環境構築プロジェクトです。
開発環境と本番環境の両方に対応したDockerfile とDocker Compose設定を提供します。

## アーキテクチャ
- **Nginx**: リバースプロキシ・静的ファイル配信
- **PHP-FPM**: Laravel アプリケーション実行環境
- **MySQL**: データベース
- **Redis**: キャッシュ・セッションストレージ

## ディレクトリ構造

```bash
ecs-laravel/
├── README.md                         # このファイル
├── .dockerignore                     # Docker ignore設定
├── docker-compose.yml                # Docker Compose設定（開発用）
├── docker-compose.production.yml     # Docker Compose設定（本番用）
├── src/                              # Laravel アプリケーション
└── docker/                           # Docker関連ファイル
    ├── app/                          # PHP-FPM (Laravel) コンテナ
    │   ├── Dockerfile                # PHP-FPM Dockerfile（開発用）
    │   ├── Dockerfile.production     # PHP-FPM Dockerfile（本番用）
    │   └── zzz-www.conf              # PHP-FPM設定ファイル
    ├── nginx/                        # Nginx コンテナ
    │   ├── Dockerfile                # Nginx Dockerfile（開発用）
    │   ├── Dockerfile.production     # Nginx Dockerfile（本番用）
    │   ├── nginx.conf                # Nginx メイン設定
    │   └── default.conf              # Nginx サイト設定
    └── mysql/                        # MySQL コンテナ
        ├── Dockerfile                # MySQL Dockerfile
        └── my.cnf                    # MySQL カスタム設定
```

## 使用方法

### 開発環境の起動
```bash
# コンテナのビルドと起動
docker-compose up -d --build

# Laravel の初期設定
docker-compose exec app composer install
docker-compose exec app php artisan key:generate
docker-compose exec app php artisan migrate

# アプリケーションへのアクセス
# http://localhost:8080
```

### 本番環境の起動
```bash
# 本番用コンテナのビルドと起動
docker-compose -f docker-compose.production.yml up -d --build

# 本番用初期設定
docker-compose -f docker-compose.production.yml exec app php artisan config:cache
docker-compose -f docker-compose.production.yml exec app php artisan route:cache
docker-compose -f docker-compose.production.yml exec app php artisan view:cache
```

### コンテナの停止・削除
```bash
# 開発環境
docker-compose down

# 本番環境
docker-compose -f docker-compose.production.yml down

# ボリュームも含めて削除
docker-compose down -v
```

## 主な設定

### Nginx
- PHP-FPM との通信: 9000番ポート (TCP)
- 静的ファイル配信の最適化
- gzip圧縮有効化

### PHP-FPM
- Laravel 最適化設定
- OPcache 有効化（本番環境）
- メモリ制限・実行時間の調整

### MySQL
- カスタム設定 (slow query log, innodb設定など)
- 文字セット: utf8mb4
- データ永続化

### Redis
- キャッシュ・セッション用
- メモリ最適化設定

## ECS デプロイ準備

### ECR へのイメージプッシュ
```bash
# AWS CLI でECR ログイン
aws ecr get-login-password --region ap-northeast-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.ap-northeast-1.amazonaws.com

# イメージのビルドとタグ付け
docker build -f docker/app/Dockerfile.production -t <account-id>.dkr.ecr.ap-northeast-1.amazonaws.com/laravel-app:latest .
docker build -f docker/nginx/Dockerfile.production -t <account-id>.dkr.ecr.ap-northeast-1.amazonaws.com/laravel-nginx:latest .

# ECR へプッシュ
docker push <account-id>.dkr.ecr.ap-northeast-1.amazonaws.com/laravel-app:latest
docker push <account-id>.dkr.ecr.ap-northeast-1.amazonaws.com/laravel-nginx:latest
```

## 環境変数

### 必要な環境変数
```bash
# Laravel
APP_NAME=Laravel
APP_ENV=production
APP_KEY=base64:xxxxx
APP_DEBUG=false
APP_URL=https://your-domain.com

# Database
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laravel
DB_PASSWORD=password

# Redis
REDIS_HOST=redis
REDIS_PASSWORD=null
REDIS_PORT=6379

# Cache
CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis
```

## トラブルシューティング

### よくある問題
1. **Permission denied エラー**
   ```bash
   # storage ディレクトリの権限修正
   docker-compose exec app chmod -R 775 storage bootstrap/cache
   ```

2. **Database connection エラー**
   ```bash
   # MySQL コンテナの起動確認
   docker-compose ps
   docker-compose logs mysql
   ```

3. **Composer install エラー**
   ```bash
   # Composer キャッシュクリア
   docker-compose exec app composer clear-cache
   ```

## 開発時の便利コマンド

```bash
# ログの確認
docker-compose logs -f app
docker-compose logs -f nginx

# コンテナ内でのコマンド実行
docker-compose exec app bash
docker-compose exec app php artisan tinker

# データベースアクセス
docker-compose exec mysql mysql -u laravel -p laravel
```