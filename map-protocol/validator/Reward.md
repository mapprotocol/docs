# Epoch Reward

   reward will be calculated and distributed in the last block of epoch. 

   Count the number of signatures of each validator and convert it into a score, which will eventually participate in the calculation of rewards.

   A fixed reward will be issued for each epoch.

   We will fix a reward ratio p to the validator.

   new_score = uptime ** exponent * adjustmentSpeed + old_score * (1 - adjustmentSpeed)
   Each validator will receive (p + mySorce) / (N * P + Sorce1 + Score2 +...ScoreN) Proportional reward.

   If the validator fails to fulfill its responsibilities, it will be punished through the punishment mechanism.

   Because the validator will distribute the reward to voter, the actual validator will receive the reward in the proportion of epochreward * Commission * score * slashinfo.

   Commission is the proportional value drawn by the validator in proportion to the total reward.

   Those excluding validators reward are for voter.  voter's reward will be obtained according to the voting proportion of voter to validator

   