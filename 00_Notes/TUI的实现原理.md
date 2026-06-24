终端运行：
```zsh
for i in {1..50}; do
  # 1. 算位置与状态（准备字符矩阵）
  chars=("⠋" "⠙" "⠹" "⠸" "⠼" "⠴" "⠦" "⠧" "⠇" "⠏")
  bar=$(printf "%-${i}s" "#")          # 生成当前长度的 # 号
  bar=${bar// /#}                      # 填充空格为 #
  spaces=$(printf "%-$((50-i))s" " ")  # 计算剩余需要的空格
  
  # 2. 发指令与画局部（核心核心！）
  printf "\e[?25l\e[2K\r\e[35m%s\e[0m [\e[32m%-50s\e[0m] %d%% Loading..." \
         "${chars[i%10]}" "${bar}${spaces}" "$((i*2))"
  
  # 3. 帧率控制
  sleep 0.05
done

# 4. 退出与状态恢复
printf "\e[2K\r\e[32m✔ Done\e[0m\e[?25h\n"
```
