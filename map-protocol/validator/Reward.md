# Epoch Reward

   Reward will be calculated and distributed in the last block of epoch. 

   Count the number of signatures of each validator and convert it into a score, which will eventually participate in the calculation of rewards.

   Each epoch will be rewarded with a fixed reward.

   Each validator will receive (p + mySorce) / (N * P + Sorce1 + Score2 +...ScoreN) Proportional reward.

   p is the fixed reward proportion shared by the validator

   The fraction is calculated by this formula: new_score = uptime ** exponent * adjustmentSpeed + old_score * (1 - adjustmentSpeed)

   If the validator fails to fulfill its responsibilities, it will be punished through the punishment mechanism.

   Because the validator will distribute the reward to voter, the actual validator will receive the reward in the proportion of epochreward * score * slashinfo * Commission.

   Commission is the proportional value drawn by the validator in proportion to the total reward.

   All voters will receive  epochreward * score * slashinfo  * (1-Commission) . 
   
   Per voter's reward will be obtained according to the voting proportion of voter to validator

   