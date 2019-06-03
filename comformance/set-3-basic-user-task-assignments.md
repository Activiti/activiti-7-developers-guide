# Scenario Set 3: User Tasks, User & Group Assignments visibility/access


- [User Task 1 assigned to User 1 followed by User Task 2 with Candidate Group 1]
  - User 1 should see User Task which is assigned 
  - User 1 should be able to complete his/her task
  - User 1 and User 3 should see the next task where they are candidates
  - User 2 should never see any task

- [User Task 1 with Candidate Group 1 followed by User Task 2 with Candidate Group 2]
  - User 1 should only see Task 1 

- [User Task 1 with Candidate Group 1, User 1 claim and add Candidate User 2] (UserTaskCandidateAPIsRuntimeTest)
  - User 1 should be able to claim the task
  - User 1 should be able to add candidate User 2 
  - User 1 should release the task
  - User 2 should be able to see the task as candidate

- [User Task 1 with Candidate Group 1, User 1 claim and add Candidate Group 2] (UserTaskCandidateAPIsRuntimeTest)
  - User 1 should be able to claim the task
  - User 1 should be able to add candidate User 2 
  - User 1 should release the task
  - User 2 should be able to see the task as candidate
