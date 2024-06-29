# 3.3-Python备份数据库脚本


```Python

# coding: utf-8
# author: chaney
# file  : script.py
# time  : 2024-06-04
import os
import datetime

import yaml
from obs import ObsClient


# 1. 确认需要备份的数据库
# 2. 备份数据库   mysql-01-2024-06-04.sql
# 3. 备份文件打包
# 4. 上传
# 5. 删除本地备份文件
class DbBackup(object):
    def __init__(self):
        # 获取配置信息
        self.config_file = 'config.yaml'
        fp = open(self.config_file, 'r', encoding='utf-8')
        self.cfg = yaml.load(fp, Loader=yaml.FullLoader)
        fp.close()
        self.access_key_id = self.cfg.get('access_key_id')
        self.secret_access_key = self.cfg.get('secret_access_key')
        self.endpoint = self.cfg.get('endpoint')
        self.bucket_name = self.cfg.get('bucket_name')
        self.container = self.cfg.get('container')
        self.today = datetime.datetime.now().strftime('%Y %m %d')  # 备份日期
        self.base_dir = self.cfg.get('backup_dir')
        self.backup_dir = os.path.join(self.base_dir, self.today)
        # /root/backup/2024-6-7
        os.makedirs(self.backup_dir, exist_ok=True)
        # /root/backup/2024-6-7.tar.gz
        self.backup_cmd = 'docker exec -it {} mysqldump -u {} -p{} --databases {} > {}'
        self.package_cmd = f"tar -zcvf {self.today}.tar.gz ./{self.today}"
        # tar -zcvf 2024-6-7.tar.gz  ./2024-6-7
        self.rm_cmd = f'rf -rf {self.backup_dir}'

    # 1.备份
    def backup(self, container, user, password, db_name, backup_dir):
        cmd = self.backup_cmd.format(container, user, password, db_name, backup_dir)
        os.system(cmd)

    # 2. 打包备份文件
    def package(self):
        os.chdir(self.base_dir)
        os.system(self.package_cmd)

    # 4. 上传备份文件
    def upload(self):
        print('开始上传...')
        obs = ObsClient(
            access_key_id=self.access_key_id,
            secret_access_key=self.secret_access_key,
            server=self.endpoint
        )
        #  file = /root/backup/2024-6-7.tar.gz
        file = self.backup_dir + '.tar.gz'
        resp = obs.putFile(
            bucketName=self.bucket_name,
            objectKey=self.today + '.tar.gz',
            file_path=os.path.join(self.backup_dir, file)
        )
        if resp.status < 300:
            print('上传成功！')
        else:
            print(resp.errorCode)
            print('上传失败！', resp.errorMessage)

    def remove(self):
        os.system(self.rm_cmd)

    def run(self):
        """
        主程序入口
        :return:
        """
        # 备份
        for container, setting in self.container.items():
            username = setting.get('user')
            password = setting.get('password')
            for db_name in setting.get('databases'):
                # backup_dir = /root/backup/2024-6-7/jumpserver.sql
                sql_file = os.path.join(self.backup_dir, db_name + '.sql')
                self.backup(container, username, password, db_name, sql_file)

        # 打包
        self.package()
        # 上传obs
        self.upload()
        # 删除备份目录
        self.remove()


if __name__ == '__main__':
    opt = DbBackup()
    opt.run()

```

## 2. config.yaml

```yaml
access_key_id: 'your ak'
secret_access_key: 'your sk'
endpoint: 'your endpoint'
bucket_name: 'your bucket'
backup_dir: /root/backup

container:
  jumpserver-db:
    user: root
    password: 123456
    databases: [jumpserver]
```

