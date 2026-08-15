# EX-NO-11-ELLIPTIC-CURVE-CRYPTOGRAPHY-ECC

## Aim:
To Implement ELLIPTIC CURVE CRYPTOGRAPHY(ECC)


## ALGORITHM:

1. Elliptic Curve Cryptography (ECC) is a public-key cryptography technique based on the algebraic structure of elliptic curves over finite fields.

2. Initialization:
   - Select an elliptic curve equation \( y^2 = x^3 + ax + b \) with parameters \( a \) and \( b \), along with a large prime \( p \) (defining the finite field).
   - Choose a base point \( G \) on the curve, which will be used for generating public keys.

3. Key Generation:
   - Each party selects a private key \( d \) (a random integer).
   - Calculate the public key as \( Q = d \times G \) (using elliptic curve point multiplication).

4. Encryption and Decryption:
   - Encryption: The sender uses the recipient’s public key and the base point \( G \) to encode the message.
   - Decryption: The recipient uses their private key to decode the message and retrieve the original plaintext.

5. Security: ECC’s security relies on the Elliptic Curve Discrete Logarithm Problem (ECDLP), making it highly secure with shorter key lengths compared to traditional methods like RSA.

## Program:
```
#include <stdio.h>
#include <stdlib.h>

// Structure for a point on the elliptic curve
typedef struct
{
    int x;
    int y;
    int is_infinity;
} Point;

// Elliptic curve: y^2 = x^3 + ax + b (mod p)
const int a = 2;
const int b = 3;
const int p = 17;

// Function to calculate modular inverse
int mod_inverse(int k, int p)
{
    k = k % p;

    if (k < 0)
        k += p;

    for (int x = 1; x < p; x++)
    {
        if ((k * x) % p == 1)
            return x;
    }

    return -1;
}

// Function for modulo operation
int mod(int value, int m)
{
    int result = value % m;

    if (result < 0)
        result += m;

    return result;
}

// Function to add two points
Point point_addition(Point P, Point Q)
{
    Point result;

    // P + O = P
    if (P.is_infinity)
        return Q;

    // Q + O = Q
    if (Q.is_infinity)
        return P;

    result.is_infinity = 0;

    int lambda;
    int denominator;

    // Point doubling
    if (P.x == Q.x && P.y == Q.y)
    {
        // If y = 0, result is point at infinity
        if (P.y == 0)
        {
            result.is_infinity = 1;
            return result;
        }

        denominator = mod_inverse(2 * P.y, p);

        if (denominator == -1)
        {
            result.is_infinity = 1;
            return result;
        }

        lambda = mod(
            (3 * P.x * P.x + a) * denominator,
            p
        );
    }
    else
    {
        // Point addition
        denominator = mod_inverse(Q.x - P.x, p);

        if (denominator == -1)
        {
            result.is_infinity = 1;
            return result;
        }

        lambda = mod(
            (Q.y - P.y) * denominator,
            p
        );
    }

    // Calculate resulting point
    result.x = mod(
        lambda * lambda - P.x - Q.x,
        p
    );

    result.y = mod(
        lambda * (P.x - result.x) - P.y,
        p
    );

    return result;
}

// Function for scalar multiplication (n * P)
Point scalar_multiplication(Point P, int n)
{
    Point result;

    // Start with point at infinity
    result.is_infinity = 1;
    result.x = 0;
    result.y = 0;

    Point addend = P;

    while (n > 0)
    {
        if (n & 1)
        {
            result = point_addition(result, addend);
        }

        addend = point_addition(addend, addend);

        n >>= 1;
    }

    return result;
}

// Main function
int main()
{
    // Base point G
    Point G = {5, 1, 0};

    // Scalar
    int n = 7;

    printf("Elliptic Curve: y^2 = x^3 + %dx + %d (mod %d)\n",
           a, b, p);

    printf("Base point G: (%d, %d)\n", G.x, G.y);

    Point R = scalar_multiplication(G, n);

    if (R.is_infinity)
    {
        printf("Result of %d * G: Point at Infinity\n", n);
    }
    else
    {
        printf("Result of %d * G: (%d, %d)\n",
               n, R.x, R.y);
    }

    return 0;
}
```
## Output:
<img width="1364" height="830" alt="image" src="https://github.com/user-attachments/assets/532cc521-e363-4ef9-b180-107c7c40ad22" />

## Result:
The program is executed successfully

