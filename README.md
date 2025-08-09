# これは何

## 目的
ECS でLaravel を使用したいので、dockerfile 関連の管理場所

## ディレクトリ構造

```bash
ecs-laravel/
├── README.md                         # このファイル
├── .dockerignore                     # Docker ignore設定
├── docker-compose.yml                # Docker Compose設定（開発用）
├── docker-compose.production.yml     # Docker Compose設定（本番用）
├── php-fpm-socket/                   # PHP-FPM ソケット用ディレクトリ
├── src/                              # Laravel アプリケーション（省略）
└── docker/                           # Docker関連ファイル
    ├── app/                          # PHP-FPM (Laravel) コンテナ
    │   ├── Dockerfile                # PHP-FPM Dockerfile（開発用）
    │   ├── Dockerfile.production     # PHP-FPM Dockerfile（本番用）
    │   └── zzz-www.conf              # PHP-FPM設定ファイル
    ├── nginx/                        # Nginx コンテナ
    │   ├── Dockerfile                # Nginx Dockerfile（開発用）
    │   ├── Dockerfile.production     # Nginx Dockerfile（本番用）
    │   ├── nginx.conf                # Nginx メイン設定
    │   └── default.conf              # Nginx サイト設定 (PHP-FPM 9000ポート通信)
    ├── mysql/                        # MySQL コンテナ
    │   ├── Dockerfile                # MySQL Dockerfile
    │   └── my.cnf                    # MySQL カスタム設定
    └── redis/                        # Redis コンテナ
        ├── redis.Dockerfile          # Redis Dockerfile
        └── redis.conf                # Redis 設定ファイル
```

## 主な設定

- **Nginx**: PHP-FPM との通信は9000番ポート (TCP) を使用
- **MySQL**: カスタム設定 (slow query log, innodb設定など) を適用
- **PHP-FPM**: Laravel アプリケーション用の設定
- **Redis**: キャッシュ・セッション用