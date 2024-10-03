
my-mariadb

root
vrbK19HEV8%
vrbK19HEV8

mysql -h 172.18.0.2 -P 30006 -u root -p
# Password: 1qaz@WSX3edc

selector:
app.kubernetes.io/component: primary
app.kubernetes.io/instance: my-mariadb
app.kubernetes.io/name: mariadb


NAME: my-mariadb
LAST DEPLOYED: Sun Sep 29 14:21:15 2024
NAMESPACE: dev
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: mariadb
CHART VERSION: 19.0.7
APP VERSION: 11.4.3

** Please be patient while the chart is being deployed **

Tip:

Watch the deployment status using the command: kubectl get pods -w --namespace dev -l app.kubernetes.io/instance=my-mariadb

Services:

echo Primary: my-mariadb.dev.svc.cluster.local:3306

Administrator credentials:

Username: root
Password : $(kubectl get secret --namespace dev my-mariadb -o jsonpath="{.data.mariadb-root-password}" | base64 -d)

To connect to your database:

1. Run a pod that you can use as a client:

   kubectl run my-mariadb-client --rm --tty -i --restart='Never' --image  docker.io/bitnami/mariadb:11.4.3-debian-12-r1 --namespace dev --command -- bash

2. To connect to primary service (read/write):

   mysql -h my-mariadb.dev.svc.cluster.local -uroot -p my_database

To upgrade this helm chart:

1. Obtain the password as described on the 'Administrator credentials' section and set the 'auth.rootPassword' parameter as shown below:

   ROOT_PASSWORD=$(kubectl get secret --namespace dev my-mariadb -o jsonpath="{.data.mariadb-root-password}" | base64 -d)
   helm upgrade --namespace dev my-mariadb oci://registry-1.docker.io/bitnamicharts/mariadb --set auth.rootPassword=$ROOT_PASSWORD

WARNING: There are "resources" sections in the chart not set. Using "resourcesPreset" is not recommended for production. For production installations, please set the following values according to your workload needs:
- primary.resources
- secondary.resources
  +info https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/

##

GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '1qaz@WSX3edc' WITH GRANT OPTION;

## Export Service Port 30006 to replace port -- Node Port

Refer to [svc_nodeport.yaml](svc_nodeport.yaml)

###
要為 MariaDB 中的 root 用戶創建全局 (@%) 的訪問權限，您可以通過以下步驟來實現。這將允許 root 用戶從任何遠程主機連接到 MariaDB。
步驟 1：進入 MariaDB 控制台

首先，進入 MariaDB 控制台。您可以通過在本地機器上運行以下命令登錄 MariaDB：

bash

mysql -u root -p

輸入 root 用戶的密碼後，您將進入 MariaDB 控制台。
步驟 2：創建或更新 root@% 用戶的訪問權限

接下來，創建或更新 root 用戶，並將其設置為允許從任何主機 (%) 連接：

sql

GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'your_password' WITH GRANT OPTION;

    *.*：表示允許 root 用戶對所有數據庫和所有表進行完全訪問。
    root@'%'：表示允許 root 用戶從任何主機（即 % 是一個通配符，表示任意 IP 地址）進行連接。
    your_password：這是為 root 用戶設置的密碼，請替換為實際的密碼。
    WITH GRANT OPTION：允許 root 用戶向其他用戶授予權限。

步驟 3：刷新權限

在執行完上述 GRANT 語句後，您需要刷新權限，以便 MariaDB 重新加載新的權限設置：

sql

FLUSH PRIVILEGES;

步驟 4：檢查權限

您可以使用以下命令檢查 root@% 是否擁有正確的權限：

sql

SHOW GRANTS FOR 'root'@'%';


=================
確認 MariaDB 資料庫權限

確保資料庫用戶有權限從遠程主機連接。

在 MariaDB 中創建具有遠程訪問權限的用戶：

sql

CREATE USER 'your_username'@'%' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON your_database.* TO 'your_username'@'%';
FLUSH PRIVILEGES;

'%' 表示允許從任何主機連接。如果要限制為特定 IP，可以替換為你的本地機器的 IP。
8. 檢查 MariaDB 的監聽地址

確保 MariaDB 配置為監聽所有網絡接口，而不僅僅是 localhost。

修改 MariaDB 的配置文件（如 /etc/mysql/my.cnf）：

ini

[mysqld]
bind-address = 0.0.0.0

然後重啟 MariaDB：

bash

sudo systemctl restart mariadb

注意：在 Kubernetes 中，你可能需要修改 MariaDB 的容器配置或使用 ConfigMap 來設置 bind-address。
9. 驗證資料表是否創建

一旦確認連接成功，運行 Spring Boot 應用，並在 MariaDB 中檢查是否已創建資料表。

使用 MariaDB 客戶端查看資料表：

sql

USE your_database;
SHOW TABLES;

如果表已創建，表示連接和配置都是正確的。
10. 如果仍有問題，檢查應用程序配置和依賴

    確認你的 Spring Boot 應用使用了正確的 MariaDB 驅動。

    檢查 pom.xml 或 build.gradle 中是否包含了 MariaDB 或 MySQL 的依賴。

    pom.xml 示例：

    xml

<dependency>
    <groupId>org.mariadb.jdbc</groupId>
    <artifactId>mariadb-java-client</artifactId>
    <version>2.7.4</version>
</dependency>

確保沒有版本衝突，並且驅動與資料庫版本兼容。