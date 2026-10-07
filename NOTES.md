# Patch Notes

I focused on the issues that directly affect correctness and request performance.

I fixed the task search query so archived tasks are always excluded and the status filter is applied to both title and description matches. The root cause was SQL `AND`/`OR` precedence. I also updated the matching H2 and Oracle SQL reference queries so they stay consistent with the application.

I removed the artificial `Thread.sleep()` from `TaskController`. It added avoidable latency to every search request, especially for short or empty queries, and blocked the request thread.

I added validation for `page` and `pageSize` and return a clear `400` response for invalid status values instead of allowing `Enum.valueOf()` to fail unexpectedly.

On the frontend, I fixed request state handling so errors clear when a new request starts and loading is cleared on failure. I also abort stale fetch requests so an older search response cannot overwrite a newer one.

I reset pagination when search/filter values change so users do not remain on an invalid page after narrowing the result set.

I chose not to redesign the API, introduce a service layer, or replace the current pagination model because those changes would add scope without solving a higher-value problem for this exercise.

The biggest remaining risk is that the repository currently loads all matching rows and paginates them in Java. That will not scale well as the task table grows.

I used ChatGPT to inspect the code, reason about SQL precedence and request-state behavior, and draft the patch. I verified the proposed changes against the existing request flow instead of blindly applying generated code.
