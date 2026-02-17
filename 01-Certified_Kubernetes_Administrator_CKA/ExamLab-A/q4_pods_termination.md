# Question 4 | Find Pods first to be terminated

Solve this question on: ssh cka2556

Check all available Pods in the Namespace project-c13 and find the names of those that would probably be terminated first if the nodes run out of resources (cpu or memory).

Write the Pod names into /opt/course/4/pods-terminated-first.txt.
---

### Answer:
```
k -n project-c13 describe pod | grep -A 3 -E 'Requests|^Name:'
```

```
# /opt/course/4/pods-terminated-first.txt
c13-3cc-runner-heavy-65588d7d6-djtv9map
c13-3cc-runner-heavy-65588d7d6-v8kf5map
c13-3cc-runner-heavy-65588d7d6-wwpb4map
```