# RSA

RSA

Description

RSA is the best-known public key encryption algorithm. In this algorithm each participant has a private key that is shared with no one else and a public key which is published so everyone knows it. To send a secure message to this participant, you encrypt the message using the widely known public key; the participant then decrypts the messages using his or her private key. Here is the procedure of RSA:


First, choose two different large prime numbers P and Q, and multiply them to get N (= P * Q).
Second, select a positive integer E (0 < E < N) as the encryption key such that E and T= (P - 1) * (Q - 1) are relatively prime.
Third, compute the decryption key D such that 0 <= D < T and (E * D) mod T = 1. Here D is a multiplicative inverse of E, modulo T.


Now the public key is constructed by the pair {E, N}, and the private key is {D, N}. P and Q can be discarded.


Encryption is defined by C = (M ^ E) mod N, and decryption is defined by M = (C ^ D) mod N, here M, which is a non-negative integer and smaller than N, is the plaintext message and C is the resulting ciphertext.


To illustrate this idea, let’s see the following example:
We choose P = 37, Q = 23, So N = P * Q = 851, and T = 792. If we choose E = 5, D will be 317 ((5 * 317) mod 792 = 1). So the public key is {5, 851}, and the private key is {317, 851}. For a given plaintext M = 7, we can get the ciphertext C = (7 ^ 5) mod 851 = 638.


As we have known,for properly choosen very large P and Q, it will take thousands of years to break a key, but for small ones, it is another matter.


Now you are given the ciphertext C and public key {E, N}, can you find the plaintext M?

Input

The input will contain several test cases. Each test case contains three positive integers C, E, N (0 < C < N, 0 < E < N, 0 < N < 2 ^ 62).

Output

Output the plaintext M in a single line.

Sample Input

638 5 851

Sample Output

7

密码学:RSA公钥密码

#include<stdlib.h>

#include<stdio.h>

#include<string.h>

#include<algorithm>
  
using namespace std;

typedef unsigned __int64 u64;

#define MAX 30

#define MAXN 5

u64 len, dig, limit;

u64 factor[MAXN];

u64 mod(u64 a, u64 b, u64 n){

        if(!a)return 0;
        
        else return ( ((a&dig)*b)%n + (mod(a>>len,b,n)<<len)%n )%n;
        
}

u64 by(u64 a, u64 b, u64 n){

            u64 p;
            
            p = 8, len = 61;
            
        while(p<n){
        
                    p<<=4;
                    
                    len -=4;
                    
        }
        
            dig = ((limit/p)<<1) - 1; 
            
        return mod(a,b,n);
        
}

u64 random(){//产生随机数 

            u64 a;
            
            a = rand();
            
            a *= rand();
            
            a *= rand();
            
            a *= rand();
            
        return a;
        
}

u64 square_multiply(u64 x, u64 c, u64 n){//(x^c)%n快速算法 

            u64 z=1;
            
        while(c){
        
                if(c%2==1)z = by(z,x,n);
                
                    x = by(x,x,n);
                    
                    c=(c>>1);
                    
        }
        
        return z;
        
}

bool Miller_Rabin(u64 n){//Miller_Rabin素数测试 

    if(n<2)return false;
    
        if(n==2)return true;
        
        if(!(n&1))return false;
        
            u64 k = 0, i, j, m, a;
            
            m = n - 1;
            
        while(m%2==0)m=(m>>1),k++;
        
        for(i=0;i<MAX;i++){
        
                    a = square_multiply(random()%(n-1)+1, m, n);//平方乘
                    
                if(a==1)continue;
                
                for(j=0;j<k;j++){
                
                        if(a==n-1)break;
                        
                            a = by(a,a,n);
                            
                }
                
                if(j<k)continue;
                
                return false ;
                
        }
        
        return true;
        
}

u64 gcd(u64 a,u64 b){

        if(a==0) return b;
        
        else return gcd(b%a,a);
        
}

u64 f(u64 x, u64 n ){

        return (by(x,x,n)+1)%n;
}

u64 Pollard(u64 n){

                if(n<=2)return 0;
                
                
                if(!(n&1))return 2; 
                
            u64 i, p, x,xx;
            
        for(i=0;i<MAX;i++){   
        
                    x = random()%n; //或者直接用 x = i
                    
                    xx = f(x,n);
                    
                    p = gcd((xx+n-x)%n , n);
                    
                while(p==1){
                
                            x = f(x,n);
                            
                            xx = f(f(xx,n),n);
                            
                            p = gcd((xx+n-x)%n,n)%n;
                            
                }
                
                if(p)return p;
                
        }
        
        return 0;
        
}

u64 prime(u64 a){

        if(Miller_Rabin(a))return a;
        
            u64 t = Pollard(a);
            
        if(t!=0)
        
        {return prime(t);}
        
           
}

u64 Euclid(u64 a,u64 b,__int64 &x,__int64 &y)

{

 if(b==0)
 
 { 
 
     x=1,y=0;
     
  return a;
  
 } 
 
 u64 t,d;
 
 if(b!=0)
 
 d=Euclid(b,a%b,x,y);
 
 t=x;
 
 x=y;
 
 if(b!=0)
 
 y=t-a/b*y;
 
 return d;
 
}

int main(){

            u64 c,e,n,i,j,m,t,n0,T,ans,l;
            
        __int64 T0,x,y,d;
        
            limit = 1;
            
            limit = limit<<63; 
            
        while(scanf("%I64u%I64u%I64u",&c,&e,&n)!=EOF){
        
              m=0;n0=n;
              
               while(n%2==0){n/=2;factor[m++]=2;}
               
               while(n>1){
               
                        if(Miller_Rabin(n))break;
                        
                            t = prime(n);
                            
                            factor[m++] = t;
                            
                        if(t!=0)
                        
                            n/=t;
                            
                }
                
               if(n>1)factor[m++]=n;
               
               //for(l=0;l<m;l++)printf("%I64u\n",factor[l]);
               
                   T0=(__int64)(factor[0]-1)*(factor[1]-1);
                   
          T=(u64)T0;
          
               Euclid(e,T,x,y);
               
                   d=x;
                   
      //printf("%I64d\n",d);
      
      //while(d<0)d+=T0;
      
                   d=(d%T0+T0)%T0;
                   
               //d=(__int64)d;
               
     // printf("%I64d %I64d\n",d,T0);
     
                   ans=square_multiply(c,(u64)d,n0);
                   
               printf("%I64u\n",ans);
               
                   
}

}
