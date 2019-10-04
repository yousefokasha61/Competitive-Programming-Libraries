# Number Theory
> For 32-bit the biggest prime number is (2^31) - 1\

> For 64-bit the biggest prime number is (2^61) - 1\

>The number of primes int the range [1...N] = N/log(N)\

> Wilson's Theorem: (p-1)! % p = (p-1) if p is a prime number

# Code for Checking if the number is prime or not in O(N)
```c++
typedef long long ll;

bool isPrime(ll n){
    if(n==2) return true;
    if(n < 2) return false;

    for(int i=2; i<n; ++i){
        if(n%i == 0)
            return false;
    }
    return true;
}
```
# Code for checking if the number is prime or not in O(N/2)

```c++
bool isprime2(ll n) {		// O(n)
    if(n == 2) 	return true;
    if(n < 2 || n%2 == 0) 	return false;

    for(int i=3; i < n; i+=2)
    	if(n%i == 0)
    		return false;
    return true;
}
```

# An important observation
* 36 = 1 * 36
* 36 = 2 * 18
* 36 = 3 * 12
* 36 = 4 * 9
* 36 = 6 * 6
* 36 = 9 * 4
* 36 = 12 * 3
* 36 = 18 * 2
* 36 = 36 * 1

> if n = a * b and a <= b THEN	a <= sqrt(n) and  b >= sqrt(n)

```c++
bool isprime3(ll n) {	// O( sqrt(n) ? NO
    if(n == 2) 				return true;
    if(n < 2 || n%2 == 0) 	return false;

    for(ll i=3; i <= sqrt(n); i+=2)
    	if(n%i == 0)
    		return false;
    return true;
}
```
> With some modification i < sqrt(N) (taking power of two sides)
> 
>i*i <= n

```c++
bool isprime4(ll n) {	// O( sqrt(n)

    if(n == 2) 				return true;

    if(n < 2 || n%2 == 0) 	return false;

    for(ll i=3; i*i <= n; i+=2)
    {
    	if(n%i == 0)
    		return false;
    }
    return true;
}
```

# Count number of primes in a given range O(N)
```c++
int countPrimesInRange(int n)	// Forward thinking
{	// O(n * sqrt(n) )
	total_operations = 0;
	int cnt = 0;
	for(int i = 1; i <= n; ++i)
		if( isprime4(i) )
			++cnt;

	return cnt;
}
```

# Count number of primes in a given range O(N log(log(N)))
```c++
int countPrimesInRange_sieve(int n)	// Backward thinking
{
	vector<bool> isPrime(n+1, true);	// set all of them to primes
	isPrime[0] = isPrime[1] = 0;		// 0, 1 are not primes

    for (ll i = 2; i*i <= n; ++i) {
        if (isPrime[i]) {
            for (int j = i * 2; j <= n; j += i)
            	isPrime[j] = 0;
        }
    }

    int cnt = 0;

    for (int i = 0; i < (int)isPrime.size(); ++i)
    	if(isPrime[i])
    		cnt++;

    return cnt;
}
```

# Sieve but in linear time complexity O(N)
## Algorithm:
> Our goal is to calculate minimum prime factor ***lp[i]*** for every number ***i*** in the segment [2;n].\
>Besides, we need to store the list of all the found prime numbers - let's call it ***pr[]***.\
> We'll initialize the values ***lp[i]*** with zeros, which means that we assume all numbers are prime. During the algorithm execution this array will be filled gradually.\
> Now we'll go through the numbers from 2 to *n*. We have two cases for the current number ***i***:
> * ***lp[i]***=0 - that means that ***i*** is prime, i.e. we haven't found any smaller factors for it.
> * Hence, we assign ***lp[i]=i*** and add ***i*** to the end of the list ***pr[]***.
> * ***lp[i]*** ≠0 - that means that ***i*** is composite, and its minimum prime factor is ***lp[i]***.
> 
> In both cases we update values of ***lp[]*** for the numbers that are divisible by ***i***. However, our goal is to learn to do so as to set a value ***lp[]*** at most once for every number. We can do it as follows:
> * Let's consider numbers ***xj=i⋅pj***, where ***pj*** are all prime numbers less than or equal to ***lp[i]*** (this is why we need to store the list of all prime numbers).
> * We'll set a new value ***lp[xj]=pj*** for all numbers of this form.

# Code
```c++
const int N = 10000000;
int lp[N+1];
vector<int> pr;

for (int i=2; i<=N; ++i) {
    if (lp[i] == 0) {
        lp[i] = i;
        pr.push_back (i);
    }
    for (int j=0; j<(int)pr.size() && pr[j]<=lp[i] && i*pr[j]<=N; ++j)
        lp[i * pr[j]] = pr[j];
}
```
## OR
```c++
// C++ program to generate all prime numbers 
// less than N in O(N) 
#include<bits/stdc++.h> 
using namespace std; 
const long long MAX_SIZE = 1000001; 

// isPrime[] : isPrime[i] is true if number is prime 
// prime[] : stores all prime number less than N 
// SPF[] that store smallest prime factor of number 
// [for Exp : smallest prime factor of '8' and '16' 
// is '2' so we put SPF[8] = 2 , SPF[16] = 2 ] 
vector<long long >isprime(MAX_SIZE , true); 
vector<long long >prime; 
vector<long long >SPF(MAX_SIZE); 

// function generate all prime number less then N in O(n) 
void manipulated_seive(int N) 
{ 
	// 0 and 1 are not prime 
	isprime[0] = isprime[1] = false ; 

	// Fill rest of the entries 
	for (long long int i=2; i<N ; i++) 
	{ 
		// If isPrime[i] == True then i is 
		// prime number 
		if (isprime[i]) 
		{ 
			// put i into prime[] vector 
			prime.push_back(i); 

			// A prime number is its own smallest 
			// prime factor 
			SPF[i] = i; 
		} 

		// Remove all multiples of i*prime[j] which are 
		// not prime by making isPrime[i*prime[j]] = false 
		// and put smallest prime factor of i*Prime[j] as prime[j] 
		// [ for exp :let i = 5 , j = 0 , prime[j] = 2 [ i*prime[j] = 10 ] 
		// so smallest prime factor of '10' is '2' that is prime[j] ] 
		// this loop run only one time for number which are not prime 
		for (long long int j=0; 
			j < (int)prime.size() && 
			i*prime[j] < N && prime[j] <= SPF[i]; 
			j++) 
		{ 
			isprime[i*prime[j]]=false; 

			// put smallest prime factor of i*prime[j] 
			SPF[i*prime[j]] = prime[j] ; 
		} 
	} 
} 

// driver program to test above function 
int main() 
{ 
	int N = 13 ; // Must be less than MAX_SIZE 

	manipulated_seive(N); 

	// pint all prime number less then N 
	for (int i=0; i<prime.size() && prime[i] <= N ; i++) 
		cout << prime[i] << " "; 

	return 0; 
} 
```

