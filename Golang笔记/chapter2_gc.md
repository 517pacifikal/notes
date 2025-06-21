# Golang 垃圾回收(GC)机制

## Go v1.3标记清除法

![](https://secure2.wostatic.cn/static/dMRWukWBtbMbEzJnENq9eW/image.png?auth_key=1750484794-5s5WG1d8Xk6Kjkx1P1J3vZ-0-83c94b3b599f00bd33c273fba85cf45d)

![](https://secure2.wostatic.cn/static/vbi14mNpYSNcVYPgAXKt1A/image.png?auth_key=1750484794-8JVjnBSTg2mwzZxbktaMS4-0-bb6270f3ea1dcf91095856d6fc2af0b0)

![](https://secure2.wostatic.cn/static/mwxJpXBoaaakSwe6T8TVZw/image.png?auth_key=1750484794-qqveBr5cpdMrVjpP4GnWxa-0-9dd180ce461db2e120c267b14cea954b)

优化思路

![](https://secure2.wostatic.cn/static/5zJ1vvEoiuPaA1bnBiDiE5/image.png?auth_key=1750484795-n14CqBCH1EACz2Za7YTcsY-0-359d1bcd15fec67bb070241d83afad0c)

## 三色标记法

![](https://secure2.wostatic.cn/static/5d1mRktvymDpntP69vyiEj/image.png?auth_key=1750484794-vvMA9sRNV4dCTLtwzGUXxh-0-8e1d567e75a9f0b74db0c8e38a97e4aa)

![](https://secure2.wostatic.cn/static/785emrEU2aLbhmcuBk4AdC/image.png?auth_key=1750484794-tJqsKuFL4aPKfJr1RZothP-0-8079fdaf301cbb0abafac47b66db5772)

一趟下来
  - 保留的白色结点是要回收的垃圾
  - 灰色节点是中间状态，最终会变为黑色结点
  - 黑色节点是幸存者

它是逐层逐层遍历的，而不是一次性遍历，这是它和标记清除法的根本区别。

如果没有stw保护，这种方法担心出现的情况：对象丢失

![](https://secure2.wostatic.cn/static/2n9qbgW6ULnHRpJCGgoUPn/image.png?auth_key=1750484794-bK1dBHgMi9jN8rRENAjxEg-0-4eeb27dea0bcb82168074169c176b343)

![](https://secure2.wostatic.cn/static/kUpeUbwBFFH1BaffwTHHPH/image.png?auth_key=1750484794-rkMcNmRWkYBgjZazb2mQt3-0-f945347e45d0aa4eb10dcf8493efec7c)

解决方案：破坏两个条件同时成立

## 强/弱三色不变式

![](https://secure2.wostatic.cn/static/a8TmthiXpQmT4McngJFsTk/image.png?auth_key=1750484794-8KtGZiHVkacFbJRSGV6CU3-0-90f46c24a875c3eda8beef9bbb25fe01)

![](https://secure2.wostatic.cn/static/bVXW9pmn8xsMoHWYbvw9FM/image.png?auth_key=1750484795-7DCKn11Gk5V7ySGY4bujcV-0-a3e17e5b8ed773b69badb3c0a0b566aa)

![](https://secure2.wostatic.cn/static/deH9DU4LEHN8HbkwqMuVoX/image.png?auth_key=1750484794-mzLjXoyjt9utPwzsVbNxs3-0-b8cf5a20a001f0636efa3546c679a4d2)

## 屏障机制-插入写屏障

![](https://secure2.wostatic.cn/static/q9ANArX2Jhsd7DRCEzWhyL/image.png?auth_key=1750484794-cgWNHszFKFYYmtrAjqxDPE-0-bf8683bf9837dece2437f1a6838249f4)

![](https://secure2.wostatic.cn/static/iPxfPd7Adkgrtg8qs9M2rM/image.png?auth_key=1750484794-4Strdi289cDuYkoMgHRu9s-0-6941c00ce2bc7e58856b43cb0b3e0c23)

插入屏障不在栈对象上使用，为了保证栈的速度。因为栈的空间比较小，而且对速度要求高

![](https://secure2.wostatic.cn/static/5Wcob7bxqtEzET8S2SZ6kx/image.png?auth_key=1750484795-on6M5hB6FAgWsLQfiFVwzK-0-05b735a95b03ccf3fc1ae66d85b03923)

![](https://secure2.wostatic.cn/static/mi2yeSwJ14u6UTBzaZpz1J/image.png?auth_key=1750484795-sWSyNFJfaNPxxfHaStJtTp-0-27152d16591de5f8d42b0081db894a5c)

![](https://secure2.wostatic.cn/static/aJY2qcTtwAAwZJpa5pVbR4/image.png?auth_key=1750484794-w2MHso8p9Z7KBTfiJqmbSR-0-aae9eb9f146a7aaa130ad880877c9d04)

![](https://secure2.wostatic.cn/static/cpwX5eNAA2LELpuxGthJfQ/image.png?auth_key=1750484795-nAZA4Q42Xc8KKnwh89wb1T-0-6b46465f47f3c67dfcb9996a448249cf)

**不足：**插入写屏障的不足就是需要stw来重新扫描栈。但这个stw时间已经被优化了

## 屏障机制-删除写屏障

![](https://secure2.wostatic.cn/static/89BjE7W1xR6Geyn9rxtX8R/image.png?auth_key=1750484795-bajD4z8eLaihZYGSymu41L-0-4951915044b1413d9144cf0ef322e924)

![](https://secure2.wostatic.cn/static/npV9b2u5ennz1dbTVindRC/image.png?auth_key=1750484795-eekm6W6xBXnGsJ1aPXaBxv-0-28858aa4034fd476434d5a76e6fbe467)

![](https://secure2.wostatic.cn/static/885FNhmTrTJnpt9aPiRZwn/image.png?auth_key=1750484795-6TSuNNjUkMrzKD8CFgqxep-0-25c4e95eee1abe80f3b2ec9e8a0680e4)

删除屏障会保护一轮被保护的对象

![](https://secure2.wostatic.cn/static/7YdVQyQKS8VPbyAumunf7u/image.png?auth_key=1750484795-62aDR9HKaTFqASa6n2uqnd-0-88c0f8a485193fb6cab828f902ddfb87)

## 屏障机制-混合写屏障

![](https://secure2.wostatic.cn/static/7xfzZvbuJwHAC1jxM9EySi/image.png?auth_key=1750484796-uXNjoG13fqgUGXHHehgjk6-0-4f831851228679d92cad802561808b26)

![](https://secure2.wostatic.cn/static/28m2WMAtZBJg6GoNB6k2t/image.png?auth_key=1750484796-wPc5nhjjdArhfYyLgzVywp-0-a44c2f6a972293174240be94829cf482)

![](https://secure2.wostatic.cn/static/eQpPbBr86Yjp65hd7Qxp6a/image.png?auth_key=1750484796-4ByMAFwHhBSdf7sathdWr5-0-17e54180cbb249715127feba503da724)

![](https://secure2.wostatic.cn/static/ruYzb3w3WwX9Z17oGbzCzD/image.png?auth_key=1750484796-7ZgHqPVTbUUeVXHer8wHPn-0-1ba48b7a864f2ca0ce50fce52586a644)