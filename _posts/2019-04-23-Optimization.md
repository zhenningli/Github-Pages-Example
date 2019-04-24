---
layout: post
title: Optimazation Part1
description: lingo results/integer programming
category: blog
---



## 1.Lingo output results
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

		!lingo;
		max = 72*x1 + 64*x2;

		x1 +x2<=50;
		12*x1+8*x2<=480;
		3*x1<=100;
		x1>=0;
		x2>=0;

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



- 3个约束条件可以视作3种资源： 原料、劳动时间、车间甲的加工能力。Slack and surplus 结果显示workload尚有40kg加工能力，因此不是紧约束。
- 目标函数可以看做为“效益”，当成为紧约束的“资源”一旦增加，“效益”必然跟着增长。Dual price 结果显示在最优解下“资源”增加一个单位时，“效益”的增量：即source增加1，利润增长48；time增加1，利润增长2。这里“效益”的增量可以看做“资源”的潜在价值，经济学上称为**影子价格**（shadow price），即source的影子价格为48，time的影子价格为2，workload的影子价格为0。
- 根据objective coefficient ranges，x1的系数范围为[64, 96], x2的系数范围为[48, 72]时，最优基不变，但最优解改变了。For example，cof（x1）变成90，最优基不变，但是optimum = 90 * 20 + 64 * 30 = 3720。
- 下面对“资源”的影子价格作进一步的分析，影子价格的作用（即在最优解下“资源”增加1个单位时“效益”的增量）是有限制的。 每增加1单位source利润增加48，但约束的右端项（current RHS）的允许增加和允许减少给出了影子价格有意义条件下约束右端的限制范围（因为此时最优基不变，影子价格才有意义；如果最优基已经没了，那么给出的影子价格也是不正确的）。 因此可以批准用35元买source（<48）,但每天最多买10单位；可以用低于2/h的价格来聘用临时工人以增加劳动时间，但是最多只能增加53.333h.

## 2. Lingo中的集合

![image.png](https://upload-images.jianshu.io/upload_images/4632352-b6a2b1c85bdbb021.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	!Lingo;
	Model:
	
		Sets: 
			quarters/1..4/: DEM, RP, OP, INV;
		ENDsets
		!sum(set(subscripts): formulation)
		min = @sum(quarters(i): 400*RP + 450*OP + 20*INV);
		
		@for(quarters(i): RP(i)<=40);
		!I#GT#1, I is 'greater than' 1, #GT# logic symbol
		@for(quarters(i)|I#GT#1:
			INV(I) = INV(I-1) + RP(I) + OP(I) - DEM(I););
	
		INV(1) = 10 + RP(1) + OP(1) - DEM(1);
	
		DATA:
			DEM = 40, 60, 75, 25;
		ENDdata
	
	END
	
- Lingo模型的最基本组成要素：
1. 集合段
> - 以sets: 开始，以endsets结束，用来定义必要的集合变量及其元素和属性。
> - quarters/1..100/: dem, rp, op, inv;
2. 目标与约束段
3. 数据段
> - 以data：开始，以enddata结束。 格式为： attribute = value_list;
> - 常数列表（value_list）中数据之间可以用逗号隔开，也可以用空格分开
> - 若输入语句格式为“变量名 = ？；” 可以作为**模型的input**，手动输入值， `INV(1) = A + RP(1) + OP(1) - DEM(1);`
4. 初始段
> - 以init:开始，以endinit结束。格式为：attribute = value_list;
5. 计算段
> - 处理raw数据。

		CALC:
		T_DEM = @SUM(quarters:DEM); !TotalDemand
		A_DEM = T_DEM/@SIZE(quarters); !AverageDemand
		endclac



## 2. Integer Programming

- ###### Example 2
		
		!lingo;
		max = 98 * x1 + 277 * x2 - x2^2 - 0.3 * x1 * x2 - 2 * x2^2;

		x1 + x2 <= 100;

		x1<=2*x2;

		@gin(x1); @gin(x2);
		
- ###### Results of Example 2

		Local optimal solution found.
 		Objective value:                              11744.80
  		Objective bound:                              11744.80
 	 	Infeasibilities:                              0.000000
  		Extended solver steps:                               0
  		Total solver iterations:                            26


                       Variable           Value        Reduced Cost
                             X1        66.00000           -87.80000
                             X2        34.00000           -53.20001

                            Row    Slack or Surplus      Dual Price
                              1        11744.80            1.000000
                              2        0.000000            0.000000
                              3        2.000000            0.000000


- > 在LINGO中，以“@”打头的都是函数调用。其中整形变量函数（`@BIN`,`@GIN`）和上下界限定函数（`@FREE`,`@SUB`,`@SLB`）














