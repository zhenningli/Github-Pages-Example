#Optimazation Part1
##1.Lingo output results
- **Reduced cost**: 给出最优的单纯形表中目标函数行（第一行）中变量对应的系数（即各个变量的检验数）。其中基变量的reduced cost一定为0；对于非基变量（注意：非基变量本身取值一定为0），相应的reduced cost值表示当该非基变量增加一个单位（其他非基变量保持不变）时目标函数减少的量（对于max型问题）。
- **Slack or Surplus（松弛或剩余）**：给出约束对应的松弛变量的值：若松弛变量为0，约束取等号，即为紧约束。
- **Dual Prices**: 表示当对应约束有微小变动时，目标函数的变化率。输出结果中对应于每一个约束有一个对偶价格。若其数值为p，表示对应约束中不等式右端项若增加一个单位，目标函数增加p个单位（max型问题）。显然，如果在最优解处约束正好取等号（紧约束，即起作用约束），对偶价格值才可能不是0。
- **Reports|Range**: 敏感性分析， 作用是给出“ranges in which the basis is unchanged”，即研究当目标函数的系数和约束右端项在什么范围变化（此时假定其他系数保持不变）时，最优基（矩阵）保持不变。这个部分包含两方面：
（1）**目标函数中系数的变化范围**（Objective Coefficient Ranges）：
          当系数在[current coefficient+allowable decrease, current coefficient+allowable increase] 的范围变化时，最优基、最优解保持不变。但由于系数发生了变化，所以最优值会变化。
（2）**约束右端项变化的范围**（Righthand Side Ranges）：
当系数在[current RHS+allowable decrease, current RHS+allowable increase] 的范围变化时，最优基保持不变，但是**最优解**、**最优值**发生变化。
>施工中
- ###### Example 1
```lingo
!lingo
max = 72*x1 + 64*x2;

x1 +x2<=50;
12*x1+8*x2<=480;
3*x1<=100;
x1>=0;
x2>=0;
```
- ###### Results of Example 1
             Global optimal solution found.
             Objective value:                              3360.000
             Infeasibilities:                              0.000000
             Total solver iterations:                             2


                       Variable           Value        Reduced Cost
                             X1        20.00000            0.000000
                             X2        30.00000            0.000000

                            Row    Slack or Surplus      Dual Price
                              1        3360.000            1.000000
                         SOURCE        0.000000            48.00000
                           TIME        0.000000            2.000000
                       WORKLOAD        40.00000            0.000000
                         X1_POS        20.00000            0.000000
                         X2_POS        30.00000            0.000000

		 **Ranges in which the basis is unchanged**:

                                      Objective Coefficient Ranges
                                  Current        Allowable        Allowable
                Variable      Coefficient         Increase         Decrease
                      X1         72.00000         24.00000         8.000000
                      X2         64.00000         8.000000         16.00000

                                           Righthand Side Ranges
                     Row          Current        Allowable        Allowable
                                      RHS         Increase         Decrease
                  SOURCE         50.00000         10.00000         6.666667
                    TIME         480.0000         53.33333         80.00000
                WORKLOAD         100.0000         INFINITY         40.00000
                  X1_POS              0.0         20.00000         INFINITY
                  X2_POS              0.0         30.00000         INFINITY



 >- 3个约束条件可以视作3种资源： 原料、劳动时间、车间甲的加工能力。Slack and surplus 结果显示workload尚有40kg加工能力，因此不是紧约束。
>- 目标函数可以看做为“效益”，当成为紧约束的“资源”一旦增加，“效益”必然跟着增长。Dual price 结果显示在最优解下“资源”增加一个单位时，“效益”的增量：即source增加1，利润增长48；time增加1，利润增长2。这里“效益”的增量可以看做“资源”的潜在价值，经济学上称为**影子价格**（shadow price），即source的影子价格为48，time的影子价格为2，workload的影子价格为0。
>- 根据objective coefficient ranges，x1的系数范围为[64, 96], x2的系数范围为[48, 72]时，最优基不变，但最优解改变了。For example，cof（x1）变成90，最优基不变，但是optimum = 90 * 20 + 64 * 30 = 3720。
>- 




