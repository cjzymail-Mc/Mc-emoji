
我一共订阅了3个pro账户，我分别建立了3个【.claude】文件夹：

【账户1：mc】
%USERPROFILE%\.claude-mc\projects\

【账户2：yk】
%USERPROFILE%\.claude\projects\

【账户3：xh】
%USERPROFILE%\.claude-xh\projects\


通常，在其中一个账户token消耗达到限额后，我会手工切换文件夹、并在另一个账号中继续同一个session，我的操作步骤如下：

1、假设【账户1：mc】在project1的【session#01】中执行任务，任务尚未完成，但碰到了token上限（需等5个小时后才能解除），现在我需要那么我会进行如下操作：
2、将 %USERPROFILE%\.claude-mc\projects\project1 整个文件夹，首先备份至.claude-mc\【back-up】\backup#001 文件夹中
3、将 %USERPROFILE%\.claude-mc 目录下的  【history.jsonl】和【settings.json】同样备份至.claude-mc\【back-up】\backup#001 文件夹中
4、做完备份后，现在我需要完成从【账户1：mc】到【账户2：yk】的迁移
5、首先，删除【账户2：yk】中对应的文件：
    - 删除 %USERPROFILE%\.claude\projects\project1 整个文件夹
    - 删除 %USERPROFILE%\.claude 目录下的  【history.jsonl】和【settings.json】
6、随后，将备份的project1 整个文件夹，复制到【账户2：yk】 对应得路径下：%USERPROFILE%\.claude\projects\
7、接着，将备份的【history.jsonl】和【settings.json】复制到【账户2：yk】 对应得路径下：%USERPROFILE%\.claude\

8、完成上述动作后，我可以继续在 git bash 中使用【账户2：yk】  claude --resume 来恢复 session#01，从而继续工作

你帮我写一个 python 移植脚本，来完成上述动作（1-7），步骤8我会手工执行
我希望运行该脚本，跳出一个提示框，有6个按钮：

1、【账户1：mc】--> 【账户2：yk】
2、【账户1：mc】--> 【账户3：xh】
-------------
3、【账户2：yk】--> 【账户1：mc】
4、【账户2：yk】--> 【账户3：xh】
-------------
3、【账户3：xh】--> 【账户1：mc】
4、【账户3：xh】--> 【账户2：yk】

我点击对应的按钮后，再次弹出第二个提示框：
  - 显示 对应的账户信息、文件夹路径
  - 【按钮1：是】，立即执行
  - 【按钮2：否】，返回上级菜单



