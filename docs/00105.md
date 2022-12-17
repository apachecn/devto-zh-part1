# 温和的立场与 INLA 比较

> 原文：<https://dev.to/martinmodrak/a-gentle-stan-vs-inla-comparison-2nl5>

我最近成了贝叶斯统计的超级粉丝。这非常有意义，如果你正在从数据中进行任何类型的推断，你应该检查一下，尤其是[斯坦语](http://mc-stan.org/)。没有更多的介绍，这是我的第一个关于统计主题的博客。

不久前，我看到了一篇由 Kahtryn Morrison 写的博客，名为[温柔的 INLA 教程](https://www.precision-analytics.ca/blog-1/inla)。这个博客很好，帮助我更好地欣赏 INLA。但是作为 Stan 概率语言的粉丝，我觉得将 INLA 与 JAGS 进行比较并不太相关，因为 Stan 应该——至少在理论上——比 JAGS 更快更好。在第二个例子“具有 iid 随机效应的泊松 GLM”中，我对 INLA 和 Stan 进行了比较。

**TLDR 是:**对于这个模型，Stan 的伸缩性比 JAGS 好得多，但是仍然不能伸缩到非常大的模型。同样，对于这个模型，Stan 和 INLA 给出了几乎相同的结果。似乎只有当你的模型无法用 INLA 编码时，Stan 才变得有用。

请让我知道(通过 GitHub 上的[问题),如果你发现任何错误或任何其他应该包含在这篇文章中的东西。此外，如果你在不同的机器和/或不同的种子上运行实验，让我知道结果。](https://github.com/martinmodrak/blog/issues)

以下是凯瑟琳博客中的原始数据:

| 普通 | kathryn_rjags | 凯瑟琳林拉 |
| --- | --- | --- |
| One hundred | Thirty point three nine four | Zero point three eight three |
| Five hundred | One hundred and forty-two point five three two | One point two four three |
| Five thousand | One thousand seven hundred and fourteen point four six eight | Five point seven six eight |
| Twenty-five thousand | Eight thousand six hundred and ten point three two | Thirty point zero seven seven |
| One hundred thousand | 6 个小时后觉得无聊 | One hundred and sixty-six point eight one nine |

*这篇文章的完整来源可以在[这个博客的 Github repo](https://github.com/martinmodrak/blog/blob/master/content/post/2018-02-02-stan-vs-inla.Rmd) 找到。请记住，不幸的是，安装 rstan 并不像运行 install.packages 那么简单。如果您还没有安装 RStan，请咨询[https://github . com/stan-dev/RStan/wiki/RStan-Getting-Started](https://github.com/stan-dev/rstan/wiki/RStan-Getting-Started)。*

## 模型

我们感兴趣的模型是一个简单的 GLM，具有随机效应的部分汇集:

```
y_i ~ poisson(mu_i)
log(mu_i) ~ alpha + beta * x_i + nu_i
nu_i ~ normal(0, tau_nu) 
```

## 比较

让我们建立我们的图书馆。

```
library(rstan)
library(brms)
rstan_options(auto_write = TRUE)
options(mc.cores = parallel::detectCores())
library(INLA)
library(tidyverse)
set.seed(6619414) 
```

结果存储在存储库中的文件中，这样我就可以轻松地用 blogdown 重建站点。删除缓存目录以强制完全重新运行。

```
cache_dir = "_stan_vs_inla_cache/"
if(!dir.exists(cache_dir)){
  dir.create(cache_dir)
} 
```

让我们从模拟数据开始

```
#The sizes of datasets to work with
N_values = c(100, 500, 5000, 25000)
data = list()
for(N in N_values) {
  x = rnorm(N, mean=5,sd=1) 
  nu = rnorm(N,0,0.1)
  mu = exp(1 + 0.5*x + nu) 
  y = rpois(N,mu) 

  data[[N]] = list(
    N = N,
    x = x,
    y = y
  )  
} 
```

### 测量斯坦

下面是 Stan 中的模型代码(把它放在一个文件中是很好的做法，但是我想让这篇文章是自包含的)。这几乎是对原始 JAGS 代码的 1:1 重写，只有很少的重要更改:

*   JAGS 通过精度参数化正态分布，斯坦通过标准差参数化正态分布。该模型将精度重新计算为 sd。
*   我增加了显式设置先前分布的参数作为数据的能力，这在本文后面会很有用
*   对于多层模型，Stan 使用所谓的非中心参数化效果更好。这意味着我们有了`nu_normalized ~ N(0,1), mu = alpha + beta * x + nu_normalized * nu_sigma`而不是`nu ~ N(0, nu_sigma), mu = alpha + beta * x + nu`。这给出了完全相同的推论，但却产生了 Stan 可以有效探索的几何图形。

还有一些包可以让你指定通用模型(包括这个)而不用写 Stan 代码，使用类似 R-INLA - checkout [rstanarm](http://mc-stan.org/users/interfaces/rstanarm) 和 [brms](https://cran.r-project.org/web/packages/brms/index.html) 的语法。后者更灵活，而前者更容易安装，因为它不依赖于 rstan，可以简单地用`install.packages`安装。

另请注意，Stan 开发人员会建议在 precision 上反对 Gamma(0.01，0.01)，而支持 sd 上的正态或柯西分布，请参见[https://github . com/Stan-dev/Stan/wiki/Prior-Choice-Recommendations](https://github.com/stan-dev/stan/wiki/Prior-Choice-Recommendations)。

```
model_code = "
  data {
    int N;
    vector[N] x;
    int y[N];

    //Allowing to parametrize the priors (useful later)
    real alpha_prior_mean;
    real beta_prior_mean;
    real<lower=0> alpha_beta_prior_precision;
    real<lower=0> tau_nu_prior_shape;
    real<lower=0> tau_nu_prior_rate; 
  }

  transformed data {
    //Stan parametrizes normal with sd not precision
    real alpha_beta_prior_sigma = sqrt(1 / alpha_beta_prior_precision);
  }

  parameters {
    real alpha;
    real beta;
    vector[N] nu_normalized;
    real<lower=0> tau_nu;
  }

  model {
    real nu_sigma = sqrt(1 / tau_nu);
    vector[N] nu = nu_normalized * nu_sigma;

    //taking advantage of Stan's implicit vectorization here
    nu_normalized ~ normal(0,1);
    //The built-in poisson_log(x) === poisson(exp(x))
    y ~ poisson_log(alpha + beta*x + nu); 

    alpha ~ normal(alpha_prior_mean, alpha_beta_prior_sigma);
    beta ~ normal(beta_prior_mean, alpha_beta_prior_sigma); 
    tau_nu ~ gamma(tau_nu_prior_shape,tau_nu_prior_rate);
  }

//Uncomment this to have the model generate mu values as well
//Currently commented out as storing the samples of mu consumes 
//a lot of memory for the big models
/*  
  generated quantities {
    vector[N] mu = exp(alpha + beta*x + nu_normalized * nu_sigma);
  }
*/
"

model = stan_model(model_code = model_code) 
```

下面是进行实际测量的代码。一些警告:

*   Stan 模型的编译不计算在内(您可以使用 rstanarm 避免它，否则只需要做一次)
*   将后验样本从 Stan 传输到 r 会有一些开销。对于较大的模型，这种开销是不可忽略的，但是您可以通过将样本存储在文件中并分别读取它们来消除这种开销。这里不测量开销。
*   Stan 花了超过 16 个小时来收敛最大数据量(1e5)，然后我在将后验样本装入我的计算机内存时遇到了问题。值得注意的是，R-Inla 也在我的电脑上崩溃了。最大的尺寸因此被排除在外，但我不得不得出结论，如果你在 6 个小时后感到厌倦，Stan 对于这样一个大模型是不实用的。
*   我无法让 rjags 在合理的时间内运行，所以我没有重新运行该模型的 jags 版本。

```
stan_times_file = paste0(cache_dir, "stan_times.csv")
stan_summary_file = paste0(cache_dir, "stan_summary.csv")
run_stan = TRUE
if(file.exists(stan_times_file) && file.exists(stan_summary_file)) {
  stan_times = read.csv(stan_times_file)
  stan_summary = read.csv(stan_summary_file) 
  if(setequal(stan_times$N, N_values) && setequal(stan_summary$N, N_values)) {
    run_stan = FALSE
  }
} 

if(run_stan) {
  stan_times_values = numeric(length(N_values))
  stan_summary_list = list()
  step = 1
  for(N in N_values) {
    data_stan = data[[N]]
    data_stan$alpha_prior_mean = 0
    data_stan$beta_prior_mean = 0
    data_stan$alpha_beta_prior_precision = 0.001
    data_stan$tau_nu_prior_shape = 0.01
    data_stan$tau_nu_prior_rate = 0.01

    fit = sampling(model, data = data_stan);
    stan_summary_list[[step]] = 
      as.data.frame(
        rstan::summary(fit, pars = c("alpha","beta","tau_nu"))$summary
      ) %>% rownames_to_column("parameter")
    stan_summary_list[[step]]$N = N

    all_times = get_elapsed_time(fit)
    stan_times_values[step] = max(all_times[,"warmup"] + all_times[,"sample"])

    step = step + 1
  }
  stan_times = data.frame(N = N_values, stan_time = stan_times_values)
  stan_summary = do.call(rbind, stan_summary_list)

  write.csv(stan_times, stan_times_file,row.names = FALSE)
  write.csv(stan_summary, stan_summary_file,row.names = FALSE)
} 
```

### 测量 INLA

```
inla_times_file = paste0(cache_dir,"inla_times.csv")
inla_summary_file = paste0(cache_dir,"inla_summary.csv")
run_inla = TRUE
if(file.exists(inla_times_file) && file.exists(inla_summary_file)) {
  inla_times = read.csv(inla_times_file)
  inla_summary = read.csv(inla_summary_file) 
  if(setequal(inla_times$N, N_values) && setequal(inla_summary$N, N_values)) {
    run_inla = FALSE
  }
} 

if(run_inla) {
  inla_times_values = numeric(length(N_values))
  inla_summary_list = list()
  step = 1
  for(N in N_values) {
    nu = 1:N 
    fit_inla = inla(y ~ x + f(nu,model="iid"), family = c("poisson"), 
               data = data[[N]], control.predictor=list(link=1)) 

    inla_times_values[step] = fit_inla$cpu.used["Total"]
    inla_summary_list[[step]] = 
      rbind(fit_inla$summary.fixed %>% select(-kld),
            fit_inla$summary.hyperpar) %>% 
      rownames_to_column("parameter")
    inla_summary_list[[step]]$N = N

    step = step + 1
  }
  inla_times = data.frame(N = N_values, inla_time = inla_times_values)
  inla_summary = do.call(rbind, inla_summary_list)

  write.csv(inla_times, inla_times_file,row.names = FALSE)
  write.csv(inla_summary, inla_summary_file,row.names = FALSE)
} 
```

### 检查推论

在这里，我们看到了并排比较的推论，它们在 Stan 和 Inla 之间看起来很有可比性:

```
for(N_to_show in N_values) {
  print(kable(stan_summary %>% filter(N == N_to_show) %>% 
                select(c("parameter","mean","sd")), 
              caption = paste0("Stan results for N = ", N_to_show)))
  print(kable(inla_summary %>% filter(N == N_to_show) %>% 
                select(c("parameter","mean","sd")), 
              caption = paste0("INLA results for N = ", N_to_show)))
} 
```

Table 1: Stan results for N = 100| parameter | mean | sd |
| --- | --- | --- |
| alpha | 1.013559 | 0.0989778 |
| beta | 0.495539 | 0.0176988 |
| tau_nu | 162.001608 | 82.7700473 |
Table 1: INLA results for N = 100| parameter | mean | sd |
| --- | --- | --- |
| (Intercept) | 1.009037e+00 | 9.15248e-02 |
| x | 4.971302e-01 | 1.61486e-02 |
| Precision for nu | 1.819654e+04 | 1.71676e+04 |
Table 1: Stan results for N = 500| parameter | mean | sd |
| --- | --- | --- |
| alpha | 1.0046284 | 0.0555134 |
| beta | 0.4977522 | 0.0102697 |
| tau_nu | 71.6301530 | 13.8264812 |
Table 1: INLA results for N = 500| parameter | mean | sd |
| --- | --- | --- |
| (Intercept) | 1.0053202 | 0.0538456 |
| x | 0.4977124 | 0.0099593 |
| Precision for nu | 77.3311793 | 16.0255430 |
Table 1: Stan results for N = 5000| parameter | mean | sd |
| --- | --- | --- |
| alpha | 1.009930 | 0.0159586 |
| beta | 0.496859 | 0.0029250 |
| tau_nu | 101.548580 | 7.4655716 |
Table 1: INLA results for N = 5000| parameter | mean | sd |
| --- | --- | --- |
| (Intercept) | 1.0099282 | 0.0155388 |
| x | 0.4968718 | 0.0028618 |
| Precision for nu | 103.1508773 | 7.6811740 |
Table 1: Stan results for N = 25000| parameter | mean | sd |
| --- | --- | --- |
| alpha | 0.9874707 | 0.0066864 |
| beta | 0.5019566 | 0.0012195 |
| tau_nu | 104.3599424 | 3.5391938 |
Table 1: INLA results for N = 25000| parameter | mean | sd |
| --- | --- | --- |
| (Intercept) | 0.9876218 | 0.0067978 |
| x | 0.5019341 | 0.0012452 |
| Precision for nu | 104.8948949 | 3.4415929 |

### 总结时序

在最初的博客文章中，你可以看到 Stan 比 JAGS 保持合理的运行时间更长，但是 INLA 仍然更快。Kathryn 也可能非常幸运地获得了 N = 25 000 的种子，因为她的 INLA 跑得非常快。在我(很少)的测试中，对于 N = 25 000，INLA 总是需要至少几分钟。这可能意味着凯瑟琳的 JAGS 时间也太短了。

```
my_results = merge.data.frame(inla_times, stan_times, by = "N")
kable(merge.data.frame(my_results, kathryn_results, by = "N")) 
```

| 普通 | inla_time | 站立时间 | kathryn_rjags | 凯瑟琳林拉 |
| --- | --- | --- | --- | --- |
| One hundred | 1.061742 | One point eight eight five | Thirty point three nine four | Zero point three eight three |
| Five hundred | 1.401597 | Eleven point one two | One hundred and forty-two point five three two | One point two four three |
| Five thousand | 10.608704 | Three hundred and eighty-eight point five one four | One thousand seven hundred and fourteen point four six eight | Five point seven six eight |
| Twenty-five thousand | 611.505543 | Five thousand eight hundred and seven point six seven | Eight thousand six hundred and ten point three two | Thirty point zero seven seven |

很明显，你可以多次运行以减少不确定性等。，但是这个帖子已经占用了我太多的时间，所以这个就留给别人了。

## 检测结果的质量

我也有一种预感，也许 INLA 没有 Stan 精确，但这被证明是基于一个错误。因此，在没有太多注释的情况下，我将代码放在这里进行测试。基本上，我修改了随机数据生成器，以实际上从先验中提取(这些先验被严格限制为提供与原始值相似的α，β和τ_ nu 值)。然后我给两个算法这些先验知识。我计算真实参数和点估计(平均值)之间的差异，以及找到真实参数的后验分布的分位数。如果算法给出了可能的最佳估计，这种分位数的分布应该在(0，1)上是均匀的。事实证明，INLA 和 Stan 在几乎所有的测试中都给出了几乎完全相同的结果，并且质量上的差异(对于这个特定的模型)可以忽略不计。

```
test_precision = function(N) {
  rejects <- 0
  repeat {
    #Set the priors so that they generate similar parameters as in the example above

    alpha_beta_prior_precision = 5
    prior_sigma = sqrt(1/alpha_beta_prior_precision)
    alpha_prior_mean = 1
    beta_prior_mean = 0.5
    alpha = rnorm(1, alpha_prior_mean, prior_sigma)
    beta = rnorm(1, beta_prior_mean, prior_sigma)

    tau_nu_prior_shape = 2
    tau_nu_prior_rate = 0.01
    tau_nu = rgamma(1,tau_nu_prior_shape,tau_nu_prior_rate)
    sigma_nu = sqrt(1 / tau_nu)

    x = rnorm(N, mean=5,sd=1) 

    nu = rnorm(N,0,sigma_nu)
    linear = alpha + beta*x + nu

    #Rejection sampling to avoid NAs and ill-posed problems
    if(max(linear) < 15) {
      mu = exp(linear) 
      y = rpois(N,mu) 
      if(mean(y == 0) < 0.7) {
        break;
      }
    } 
    rejects = rejects + 1
  }

  #cat(rejects, "rejects\n")

  data = list(
    N = N,
    x = x,
    y = y
  )
  #cat("A:",alpha,"B:", beta, "T:", tau_nu,"\n")
  #print(linear)
  #print(data)

  #=============== Fit INLA
  nu = 1:N 
  fit_inla = inla(y ~ x + f(nu,model="iid",
                  hyper=list(theta=list(prior="loggamma",
                                        param=c(tau_nu_prior_shape,tau_nu_prior_rate)))), 
                  family = c("poisson"), 
                  control.fixed = list(mean = beta_prior_mean, 
                                       mean.intercept = alpha_prior_mean,
                                       prec = alpha_beta_prior_precision,
                                       prec.intercept = alpha_beta_prior_precision
                                       ),
             data = data, control.predictor=list(link=1)
             ) 

  time_inla = fit_inla$cpu.used["Total"]

  alpha_mean_diff_inla = fit_inla$summary.fixed["(Intercept)","mean"] - alpha
  beta_mean_diff_inla = fit_inla$summary.fixed["x","mean"] - beta
  tau_nu_mean_diff_inla = fit_inla$summary.hyperpar[,"mean"] - tau_nu

  alpha_q_inla = inla.pmarginal(alpha, fit_inla$marginals.fixed$`(Intercept)`)
  beta_q_inla = inla.pmarginal(beta, fit_inla$marginals.fixed$x)
  tau_nu_q_inla = inla.pmarginal(tau_nu, fit_inla$marginals.hyperpar$`Precision for nu`)

  #================ Fit Stan
  data_stan = data
  data_stan$alpha_prior_mean = alpha_prior_mean
  data_stan$beta_prior_mean = beta_prior_mean
  data_stan$alpha_beta_prior_precision = alpha_beta_prior_precision
  data_stan$tau_nu_prior_shape = tau_nu_prior_shape
  data_stan$tau_nu_prior_rate = tau_nu_prior_rate

  fit = sampling(model, data = data_stan, control = list(adapt_delta = 0.95)); 
  all_times = get_elapsed_time(fit)
  max_total_time_stan = max(all_times[,"warmup"] + all_times[,"sample"])

  samples = rstan::extract(fit, pars = c("alpha","beta","tau_nu"))
  alpha_mean_diff_stan = mean(samples$alpha) - alpha
  beta_mean_diff_stan = mean(samples$beta) - beta
  tau_nu_mean_diff_stan = mean(samples$tau_nu) - tau_nu

  alpha_q_stan = ecdf(samples$alpha)(alpha)
  beta_q_stan = ecdf(samples$beta)(beta)
  tau_nu_q_stan = ecdf(samples$tau_nu)(tau_nu)

  return(data.frame(time_rstan = max_total_time_stan,
                    time_rinla = time_inla,
                    alpha_mean_diff_stan = alpha_mean_diff_stan,
                    beta_mean_diff_stan = beta_mean_diff_stan,
                    tau_nu_mean_diff_stan = tau_nu_mean_diff_stan,
                    alpha_q_stan = alpha_q_stan,
                    beta_q_stan = beta_q_stan,
                    tau_nu_q_stan = tau_nu_q_stan,
                    alpha_mean_diff_inla = alpha_mean_diff_inla,
                    beta_mean_diff_inla = beta_mean_diff_inla,
                    tau_nu_mean_diff_inla = tau_nu_mean_diff_inla,
                    alpha_q_inla= alpha_q_inla,
                    beta_q_inla = beta_q_inla,
                    tau_nu_q_inla = tau_nu_q_inla
                    ))
} 
```

实际上是在做比较。在某些场合，斯坦不收敛，我最好的猜测是，数据在某种程度上是病态的，但我没有彻底调查。您可以看到 Stan 和 Inla 的结果非常相似，无论是点估计还是后验分位数的分布。随着数据的增多，INLA 近似值的精度也有望提高。

```
library(skimr) #Uses skimr to summarize results easily
precision_results_file = paste0(cache_dir,"precision_results.csv")
if(file.exists(precision_results_file)) {
  results_precision_df = read.csv(precision_results_file)
} else {
  results_precision = list()
  for(i in 1:100) {
    results_precision[[i]] = test_precision(50)
  }

  results_precision_df = do.call(rbind, results_precision)
  write.csv(results_precision_df,precision_results_file,row.names = FALSE)
}

#Remove uninteresting skim statistics
skim_with(numeric = list(missing = NULL, complete = NULL, n = NULL))

skimmed = results_precision_df %>% select(-X) %>% skim() 
#Now a hack to display skim histograms properly in the output:
skimmed_better = skimmed %>% rowwise() %>% mutate(formatted = 
     if_else(stat == "hist", 
         utf8ToInt(formatted) %>% as.character() %>% paste0("&#", . ,";", collapse = ""), 
         formatted))  
mostattributes(skimmed_better) = attributes(skimmed)

skimmed_better %>% kable(escape = FALSE) 
```

略读汇总统计

n obs: 100

n 变量:14

变量类型:数字

| 可变的 | 意思是 | 存储卡 | p0 蛋白 | p25 | p50 | p75 | p100 | 嘘 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 阿尔法平均值差值积分 | -0.0021 | Zero point two | -0.85 | -0.094 | 0.0023 | Zero point zero nine five | Zero point five three | ▁▁▁▂▇▇▁▁ |
| 阿尔法平均值差值标准 | -0.0033 | Zero point two | -0.84 | -0.097 | -0.00012 | Zero point zero nine three | Zero point five two | ▁▁▁▂▇▇▁▂ |
| 阿尔法 _ q _ 因拉 | Zero point five | Zero point two nine | 0.00084 | Zero point two five | Zero point five | Zero point seven three | Zero point nine nine | ▅▇▇▆▇▆▆▇ |
| 阿尔法 _ q _ 斯坦 | Zero point five | Zero point two eight | Zero point zero zero one | Zero point two six | Zero point five | Zero point seven three | Zero point nine nine | ▅▇▇▆▇▆▆▇ |
| 贝塔平均差因拉 | -0.00088 | Zero point zero four | -0.12 | -0.016 | -0.001 | Zero point zero one four | Zero point one seven | ▁▁▃▇▂▁▁▁ |
| 贝塔平均差标准 | -0.001 | Zero point zero four | -0.12 | -0.016 | -5e-04 | Zero point zero one four | Zero point one six | ▁▁▂▇▂▁▁▁ |
| 贝塔系数 | Zero point five one | Zero point two eight | 0.0068 | Zero point two six | Zero point five two | Zero point seven five | one | ▆▆▅▆▇▅▆▆ |
| 贝塔 _ q _ 斯坦 | Zero point five one | Zero point two eight | 0.0065 | Zero point two seven | Zero point five one | Zero point seven five | one | ▆▆▅▇▆▅▆▆ |
| tau_nu_mean_diff_inla | Four point four five | Ninety point one seven | -338.58 | -26.74 | Four point four nine | Fifty-three point three eight | One hundred and ninety-three | ▁▁▁▂▅▇▃▂ |
| tau_nu_mean_diff_stan(缺省值) | Five point two one | Ninety | -339.89 | -24.62 | Four point two nine | Fifty-four point four eight | One hundred and ninety-one point nine four | ▁▁▁▂▅▇▃▂ |
| 陶努因拉 | Zero point five three | Zero point two six | Zero point zero two three | Zero point three two | Zero point five two | Zero point seven four | Zero point nine nine | ▃▅▆▆▇▆▅▅ |
| tau_nu_q_stan | Zero point five three | Zero point two six | Zero point zero two one | Zero point three two | Zero point five three | Zero point seven five | Zero point nine nine | ▃▅▅▆▇▃▅▅ |
| 时间 _ 林拉 | Zero point nine seven | Zero point zero nine three | Zero point eight six | Zero point nine one | Zero point nine three | Zero point nine eight | One point three two | ▇▇▂▁▁▁▁▁ |
| 时间斯坦 | One point seven nine | One point four | Zero point five five | Zero point eight nine | One point four five | Two point zero nine | Ten point zero four | ▇▂▁▁▁▁▁▁ |