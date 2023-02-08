# Lab Report2
---
## CODE
In first method, the main parts is the part in the first else if segment. This part of code finds keyword"add-message" and add the keywords after "=" to the parameter list of current server. Everytime an item is added to the list, the server is supposed to print out all the elements added to the list. So I use a for loop to add every elements and "\n" to the string and return that String. And that is the final out put of that url.

`idx:` number of arguments already in the components list

`comp` array that store the elements added to the server

`res` the String that contains all the elements added and the newly added element, with a "\n" after each element

```
import java.io.IOException;
import java.net.URI;

class Handler implements URLHandler {
    // The one bit of state on the server: a number that will be manipulated by
    // various requests.
    int num = 0;

    String[] comp = new String[100000];
    int idx = 0;
    public String handleRequest(URI url) {
        if (url.getPath().equals("/")) {
            String listing = "";
            if (idx == 0) return "Nothing added yet";
            for (int i = 0; i < idx; i++){
                listing += comp[i];
                listing += " ";
            }
            return listing;
        } 
        else if (url.getPath().contains("/add-message")) {
            String[] parameters = url.getQuery().split("=");
            if (parameters[0].equals("s")) {
                // num += Integer.parseInt(parameters[1]);
                comp[idx++] = parameters[1];
                String res = "";
                for (int i = 0; i < idx; i++){
                    res += comp[i];
                    res += "\n";
                }
                return String.format("%s", res);
            }
        }
        return "404 Not Found";
    }
}

public class StringServer {
    public static void main(String[] args) throws IOException {
        if(args.length == 0){
            System.out.println("Missing port number! Try any number between 1024 to 49151");
            return;
        }

        int port = Integer.parseInt(args[0]);

        Server.start(port, new Handler());
    }
}
```

The main method of this class enable the server to run while enable this server with function described above


## Screenshots of results
1. `idx:` number of arguments already in the components list

2. `comp` array that store the elements added to the server

3. `res` the String that contains all the elements added and the newly added element, with a "\n" after each element

![Image](3.png)
in the screenshot above, both the urlhandler method and the main method are triggered. After inputping the url with *add-message?s=...* the url handler will add element after "s=" to the comp list.And a for loop will put every element together and put them into a String res. and res will be print.
In this Image, following are the change of variables.
**before change**
---
* `idx`:0
* `comp`:{}
* `res`:""
---
**after change**
* `idx`:1
* `comp`: {"hello"}
* `res`:"hello\n"

Also the function *getPath()* and *getQuery()* are triggered to handle the url put into the search engine.
---
![Image](4.png)
This image follows the exact same process as the upper one.
**before change**
---
* `idx`:1
* `comp`: {"hello"}
* `res`:""
---
**after change**
* `idx`:2
* `comp`: {"hello", "how are you"}
* `res`:"hello\nhow are you\n"

Also the function *getPath()* and *getQuery()* are triggered to handle the url put into the search engine.

# failure inducing input of Code

```
static void reverseInPlace(int[] arr) {
  for(int i = 0; i < arr.length; i += 1) {
    arr[i] = arr[arr.length - i - 1];
  }
}

static int[] reversed(int[] arr) {
  int[] newArray = new int[arr.length];
  for(int i = 0; i < arr.length; i += 1) {
    arr[i] = newArray[arr.length - i - 1];
  }
  return arr;
}
```
1, The problem of the `reverseInPlace()` is that it swap the array two times, which means the array becomes the original one. The correct way is to loop through the front half of the array. This will make sure that every element get swap exactly once.
2, The issue of the `reversed()` is that it returns **arr[]**, while it should return newArray[]. The point of this function should be to reverse the original array using a new array. However, the method mistakenly change the original array to value of the newArray, which contains no value at first. That is why it doesn't work as intended.

## Testcase that induce eror
```
@Test 
public void testReverseInPlace2() {
  int[] input1 = {1,2,3,4};
  ArrayExamples.reverseInPlace(input1);
  assertArrayEquals(new int[]{4,3,2,1}, input1);
}

@Test
public void testReversed2() {
  int[] input1 = {1,2,3,4,5};
  assertArrayEquals(new int[]{5,4,3,2,1}, ArrayExamples.reversed(input1));
}
```

## Testcases that doesn't induce error
```
@Test 
public void testReverseInPlace() {
  int[] input1 = { 3 };
  ArrayExamples.reverseInPlace(input1);
  assertArrayEquals(new int[]{ 3 }, input1);
}


@Test
public void testReversed() {
  int[] input1 = { };
  assertArrayEquals(new int[]{ }, ArrayExamples.reversed(input1));
}
```

## Symptoms
**didn't reverse the list as expected**
![Image](6.png)
From the test failure above, I observe that the list is not reversed with the for loop successfully implemented, but correct when there is only one element. So I think it might be the problem of the time of swap of each elements is two, which swap them to the correct position and swap them back after that. So I changed the number of time that the loop should go, and it work perfectly now

**didn't reverse the list as expected**
![Image](7.png)
This method only passes when there is no element in the list to be reversed. That means this function doesn't work at all. This is probably the problem of wrong argument. So I observe that the original code is reversing empty list to parameter list, which is completely non-sense. So I changed the return list ot newArray and change the arr to be modified in the for loop to newArray. That would make sure we make changes to the list that is supposed to be changed. Problem is therefore solved.



## Bugs fixing
**ReverseInPlace fixing strategy**
*Before fixing the code is shown as below*
```
// Changes the input array to be in reversed order
static void reverseInPlace(int[] arr) {
  for(int i = 0; i < arr.length; i += 1) {
    arr[i] = arr[arr.length - i - 1];
  }
}
```

*After fixing the code is shown as below*
1. Added a temp that ensure proper value swap of two elements
2. divided the loop time by two so that it won't swap every element for two times
```
static void reverseInPlace(int[] arr) {
    for(int i = 0; i < arr.length/2; i += 1) {
      int temp;
      temp = arr[arr.length-i-1];
      arr[arr.length-i-1] = arr[i];
      arr[i] = temp;
    }
  }
```

**Reversed function Fixing**
*Before fixing the code is shown as below*
```
static int[] reversed(int[] arr) {
  int[] newArray = new int[arr.length];
  for(int i = 0; i < arr.length; i += 1) {
    arr[i] = newArray[arr.length - i - 1];
  }
  return arr;
}
```

*After fixing the code is shown as below*
1. The returned array and modified array should be newArray but not arr. After changing that, the program runs fine.
```
static int[] reversed(int[] arr) {
  int[] newArray = new int[arr.length];
  for(int i = 0; i < arr.length; i += 1) {
    newArray[i] = arr[arr.length - i - 1];
  }
  return newArray;
}
```


# Summary
In week 2 and 3's lab report, I learn to handle http request and get information from the url. Beside that, I learn to write test cases using Junit test and debug respectively. It is hard sometimes, but I learnt a lot.
