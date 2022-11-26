---
aliases:
- /cysa/math/security/2021/02/20/Luhns-Algorithm-Credit-Card
categories:
- cysa
- security
- math
date: '2021-02-20'
description: What is Luhn's Algorithm?
layout: post
title: Credit Card Security
toc: true

---

## How Secure is a Credit Card?
Everyone is worried about having their credit card information stolen at some point. We all trust the banks to ensure nobody can just guess our number and submit orders in our name. But, did you know math is the secret behind your security? There are four main issues with stealing a credit card:
1. Getting the Credit Card Number
2. Getting the Security Code
3. Getting the Expiration Date
4. Getting your Personal Details

## Getting the Credit Card Number
There are a total of 10000000000000000 total numbers that can exist for a credit card number. The odds, therefore, to get your number is 1:10000000000000000. But, it's worse than that because the entire collection of numbers are not used to generate credit cards. In fact, credit cards can pass or fail passed on something called [Luhn's Algorithm](https://en.wikipedia.org/wiki/Luhn_algorithm) This algorithm is used to create what is called a subgroup inside of the total number of cards. We don't want criminals to just guess numbers and accidentally hit anyone. This is formula is a first line of defense to ensure fake numbers cannot be submitted.

```python
ALGORITHM_CONSTANT = 9

def confirmLuhn(number):                                             #==== Luhn's Algo ==========================
	count = 0                                                    # Sum all numbers excluding the last number.
	sum   = 0                                                    # For every other number, square it.
                                                                     # Compare the last digit of the CC number
	for digit in number:                                         # against the last digit of the sum.
		num = int(digit)
		if   count == 15:                                    # If final digit,
			return (sum *ALGORITHM_CONSTANT %10 == num)  # compare final digits.
		elif count % 2 == 1:                                 # If off number,
			sum   += num*num                             # square number.
			count +=1
		else:                                                # If not off number,
			sum   += num                                 # just add the number to the sum
			count += 1                                   #===========================================


outfile = open("CCNumbers.txt", 'w')                          # File opening, obviously.
                                                              # First we generate the number space that we're testing.
for x in range(0, 0000000000000100):                          # The real CC number space is actually [0, 9999999999999999], but that's too much.
	xString = "%.16d" % x                                 # This is probably lazy design, but I want to fill out the space of numbers and keep the 0's
	if confirmLuhn(xString): outfile.write(xString+"n")  # If it's one of them, then write the result to the file.

outfile.close()                                               # BILLIONS OF YEARS OF WORK AND YOU FORGOT TO CLOSE THE STORAGE FILE IN THE CODE?!?

```

I wrote a basic CC number finder that uses a *very* small space of numbers to check against as existing numbers - and, you can find it [here](https://github.com/Shoklan/LuhnAlgorithm)for those wishing to pull it and build on it.

## Getting the Security Code
The probability of getting the 3 digit number on the back of that card is 1:1000. That might not seem like a lot, and it's not, but without that number you can't use the card online. And, most banks will lock your card after a few failed tries. If I can see and memorize the 16 digit Credit Card number from seeing once - this isn't all that hard, it just takes a bit of practice - this 1:1000 probability is what keeps them from using it online without your consent.

Let's assume that I did memorize your number and I'm trying to figure out what that code is. Most banks I know of will lock a hard after 5 failed attempts. That means I have a 5:1000 or 1:200 chance of guessing right before the card number becomes useless. Of course, that's completely ignoring if I don't have the number and am just trying to guess everything. Even if I beat the 1:10000000000000000 chance of picking the right number **and** it passes the Luhn Algorithm **then** I still need to guess with a 1:1000 chance of getting the right number within each potential number. But, it gets even *worse* for me.

## Getting the Expiration Date
I still have to get past your expiration date. There really is no good way to know the actual probability since banks can use any number of years before the card expires. But, I'll assume a larger case to overestimate since banks will make the task as hard as possible. Let's guess that the range of expiration dates is from 2000 - 2030 since some cards are in the wild. We're looking at a 1:360 chance - which is really tiny when you compare it to other probability spaces. But, we need to think about the entire probability space of 1:10000000000000000 * 1:200 * 1:360 just to find the right card.
To select the right card data of just you is 1:720,000,000,000,000,000,000.
For comparison, that's about 6 multiples large than the estimated total of every human being ever alive.

But, what if I memorized all of this information?

## Getting your Personal Details
Even after selecting the right credit card with the right security code and the right expiration date you *still* need to know the precise living address of the person you're trying to attack to submit orders online. No wonder credit card scammers steal them with skimmers or fake online bank pages!
Math wins.
