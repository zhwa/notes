#### Notes on lessons of Python

###### Pointer of object

If you want a pointer of object, ALWAYS point to the obj itself. Never point to its attributes.

For example, the following code creates a linked list:

		# class ListNode(object):
		#     def __init__(self, x):
		#         self.val = x
		#         self.next = None


		if res == None:
                res = ListNode(digit)
                current = res
        else:
                current.next = ListNode(digit)
                current = current.next


That's correct. But the following is incorrect:

		if res == None:
                res = ListNode(digit)
                current = res.next
        else:
                current = ListNode(digit)
                current = current.next


In the second code, the pointer "current" points to an attribute (default "None"). When you want to modify "None" to another object, the Pointer actually points to a new obj, rather than modify the attribute. 


##### Int overflow
Python will handle overflow automatically, but you should be aware:

For 32 bits machine, the range of integer is: -2**32 ---> 2**32 -1 . If an integer is larger than this range, Python will transform it into Long.