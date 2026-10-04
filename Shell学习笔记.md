## 我的shell学习笔记
- **学习路径**

通过deepseek的推荐在B站上找到了网课，学习了shell控制流程的for/while循环和case/if分支，似乎比正常的编程语言简单很多。

- **学习成果**

自己编写了一段找奇偶的简单脚本，用到了for和if循环。
```bash                                                       
> #!/bin/bash                                                                                                           
> for ((i=1;i<=20;i++))                                                                                                 
> do                                                                                                                    
>   if [ $((i%2)) -eq 0 ]                                                                                               
>   then                                                                                                                
>       echo "$i是偶数"                                                                                                 
>   else                                                                                                                
>       echo "$i是奇数"                                                                                                 
>   fi                                                                                                                  
> done                                                                                                                  
> EOF
```

- **学习坎坷**

视频里没有提到嵌套的教学，我直接根据编程语言的相关经验进行嵌套尝试，有点错误但是让deepseek帮我成功纠正了。