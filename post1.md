# Introduction

First - an apology for poor formatting of the code examples - in the end I was defeated by blogger.com, so I'll be looking for another blog site ... this is just too hard to get formatting right!

# Back to Testing

It is quite a while ago since I blogged about testing (or about anything else ...) but my last post on the subject  concluded that AssertJ was the best option, for me at least.

I was using AssertJ with Mockito and the Mycila runner to support Guice. I was generally happy with that combination, although there were one or two odd syntactical problems in the transition from Java 7 to 8.

And then there was Spock.

## First thoughts

I'm not even sure why I tried Spock in the first place - I didn't think there was anything especially wrong with the libraries I was using.

I also have to admit I am not a huge fan of Groovy in itself, although I am using Gradle (also written in Groovy of course) more and more.  The power of Groovy, though, really comes to the front when it provides a well designed DSL.

But try it I did ... so here's a quick overview of my experience, based on several months of use.  I am not trying to cover all of Spock's functionality here, but I can say that I have not found anything yet which the AssertJ/Mockito combination could do that Spock cannot do.

## The "classic" approach
Using the AssertJ/Mockito approach, a trivial test might look like this
```java
@Test
public void doTest() {

    //given
    objectUnderTest.prepare(5);
    when(mockCollaborator.getStatus()).thenReturn("x");

    //when    
    objectUnderTest.doSomething();

    //then
    assertThat(objectUnderTest.getState()).isEqualTo("OK");
    assertThat(objectUnderTest.isActive()).isTrue();
    verify(mockCollaborator, times(2)).getStatus();
}
```
## The Spock approach

The equivalent in Spock would look like this:
```groovy
def "prep value 5, Out returns OK and calls collaborator twice"(){
    given:
    objectUnderTest.prepare(5);
    
    when:
    objectUnderTest.doSomething();
    
    then:
    objectUnderTest.getState().equals('OK')
    objectUnderTest.isActive()
    2 * mockCollaborator.getStatus() >> 'x'
}
```

There are a few things to note:
- The name of the test, which also appears in the test results, is far more expressive
- The given / when / then causes are part of the DSL, not just comments, and will tell you if you have got it wrong
- The static imports have all gone
- The comparison methods in the 'then' clause are all 'native'
- The last line of the 'then' clause is an example of the one thing that may trip you up.  It is saying that the mock is expecting to be called twice, and will return a value of 'x' on both occasions.

Spock processes mock interactions in a different way to Mockito, and that can cause some misunderstandings when migrating from one to the other.  It is worth reading that part of the documentation thoroughly.

## Not much difference?
At a quick look, apart from being neater, there does not seem to be a massive difference.  In practice, though, I have found that neatness very productive ... even with code completion, 'assertThat  .. isEqualTo' is a bit cumbersome, where using the native comparison methods is far more fluent.

The failure output from Spock is also an improvement on the classic approach, giving a better indication which part of a check has failed:
```
Condition not satisfied:

stack.size() == 2
|     |      |
|     1      false
[push me]
```
The only thing I miss are the collection methods of AssertJ - containsOnly(), containsExactly() - for example.  But if I miss them too much, I can still use them in Spock.

Oh, and the data driven tests are neat ...

# Conclusion

Spock seems to be one of those tools which grows on you very quickly.  At first, it seemed that its advantages were not that great, but after just a few days, the increase in fluency and productivity convinced me that the change would be worthwhile.

I don't generally convert existing tests unless they need modification, but conversion is not difficult either.

Now,it is all I use now in Krail for any new unit, and usually integration testing, and on the rare occasion I add a test method to existing "classic" tests, veryhting seems very clunky.

The learning curve is not steep - I just found that the way interactions are used take a little bit of getting used to, but the end result is extremely powerful.

If you are writing tests - I would strongly recommend that you give it a go.  And if you are not writing tests ... well, it might be a very good idea to start :-)


----------
