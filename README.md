Output?
Andy
😲 Wait...
Didn't we just say Java is pass by value?
Yes!
So why did it change?
The Secret
The variable st stores a reference to the object.
Java copies that reference value.
main()

st -----------> Student Object
                  name = John
When we call:
change(st);
Java copies the reference.
main()              change()

st ----\
         \
          -----> Student Object
         /
s ------/
Both st and s point to the same object.
So when we do:
s.name = "Andy";
We're modifying the same object.
Output:
Andy
