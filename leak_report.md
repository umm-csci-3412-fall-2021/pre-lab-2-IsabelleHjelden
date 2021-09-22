# Leak report

By using the command 'valgrind --leak-check=full ./check_whitespace'
I was able to determine that there is one memory leak in check_whitespace
that is possibly coming the functions strip (line 41), is_clean (line 62),
or main (line 87).

After looking through check_whitespace.c, I thought that 
the leak is most likely coming from the function strip. 
As on line 41, calloc is being used, which is where
the memory is being allocated to.
Changes I made:
	on line 48, before 'return result;'
	I added 'free ( result );'
However, after running valgrind with this, while there were 
no leaks possible, the error summary concluded that there
were 32 errors from 2 contexts, which I dont think is right.

If we look at lines 32-37 there is an if statement with comments
describing that the if statement checks if the string is nothing but spaces
and returns an empty string. Like how it states in the pre-lab write
up, this is the tricker memory leak. The memory is being allocated in
the function strip, but we can't free that memory yet because that
would invalidate the memory being returned in is_clean. This means we
can free the memory after cleaned is done being used in the function is_cleaned.
However, we have to check that the cleaned is not an empty string by 
putting free(cleaned) in an if statement, because if there is no memory
being used (like in an empty string) no action will occur.
