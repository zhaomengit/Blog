
'''python
i = 1

def testyield():
    while True:
        global i
        print("start --", i)
        n = yield

        print('n is --', n)
        i += 1
        print('end --', i)



l = testyield()
l.send(None)
l.send(10)
'''
