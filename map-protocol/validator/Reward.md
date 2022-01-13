# Epoch Reward
1.validator在endEpoch块进行发奖励
2.lookupwindow and 记录每一个validator签名次数 进行计算分数 sorce
3.固定发放epochReward  
4.每一个validator将收到  p*sorce1/n*p+sorce1+score2.... 比例的奖励
5.因为validator要将奖励分给voter 实际validator将收到 epochReward*commition*score*slashInfo比例的奖励
6.剩下的将按照voter给validator投票比例分给voter












