Fixed ``TimerContext.timeout()`` cancelling a task whose current await
had already resolved with a real result (e.g. a response that fully
arrived while the event loop was blocked past the ``ClientTimeout(total=)``
deadline by a long synchronous call on the same loop). ``timeout()`` now
walks each task's chain of already-resolved awaits, re-checking on a later
loop iteration, and only cancels once it reaches a genuinely pending await
or the task finishes. A task's own ``CancelledError`` is only converted to
``TimeoutError`` when this timer actually cancelled it, so an unrelated
cancellation on the same task is never mislabeled, and a task that later
does unrelated work after leaving the ``with timer:`` block is left alone.
See related issues :issue:`7864` and :issue:`7479`.
