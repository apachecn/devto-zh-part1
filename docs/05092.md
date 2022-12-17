# C++中的高斯-乔丹

> 原文：<https://dev.to/jcharliegarciam/gauss-jordan-in-c>

所以，几天前，我们大学的数值分析老师给我们留下了一个项目，编写一个解方程的数学方法。

起初，我想用单一的方法来解决这个问题，但这有什么意思呢？所以在用有趣的方式完成作业后(我有点书呆子气)，我最终用 C++和 CUDA 编程完成了这个解决方案。

注意，我真的不知道如何在 CUDA 中编码，但这不是真正的工作，所以从错误中学习是可以的。

这是我第一次发布我的代码，所以能看到你们的反馈真的很好。

这是我在 C++中解决这个问题的方法

```
void jordan(double** matrix, double **answers, int matrixSize) { //Gauss-Jordan
//clock_t begin = clock();
double temp = 0;
for (int i = 0; i < matrixSize; i++) { //Recorre diagonalmente el arreglo - Goes in a diagonal way throught the matrix
    temp = matrix[i][i];
    for (int x = 0; x < matrixSize; x++) { //Hace 1 la diagonal ([i][i]) y divide al resto de la fila - Makes 1 the number in [i][i] and divides the row 
        matrix[i][x] = matrix[i][x] / temp;
    }
    answers[i][0] = answers[i][0] / temp;
    double multiplyFactor = 0;
    for (int y = 0; y < matrixSize; y++) {
        if (y != i) {
            multiplyFactor = matrix[y][i];
            for (int x = i; x < matrixSize; x++) { //Recorre la fila haciendo 0 las coordenadas en la columna de [i][i] - Makes 0 the numbers in the column of [i][i]
                matrix[y][x] = matrix[y][x] - (matrix[i][x] * multiplyFactor);
            }
            answers[y][0] = answers[y][0] - (answers[i][0] * multiplyFactor);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是在 CUDA 中

```
__global__ void jordanGPU(long double ** matrix, long double * answer, int i)
{
    int idx = threadIdx.x;
    if (idx != i) {
            double multiplyFactor = matrix[idx][i];
            for (int x = i; x < 350; x++) { //Recorre la fila haciendo                     0 las coordenadas en la columna de [i][i]
            matrix[idx][x] = matrix[idx][x] - (matrix[i][x] *         multiplyFactor);
        }
    answer[idx] = answer[idx] - (answer[i] * multiplyFactor);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

```
clock_t begin = clock();
    double temp = 0;
    for (int i = 0; i < matrixSize; i++) { //Recorre diagonalmente el arreglo
        temp = matrix[i][i];
        for (int x = 0; x < matrixSize; x++) { //Hace 0 la diagonal ([i][i]) y divide al resto de la fila
            matrix[i][x] = matrix[i][x] / temp;
        }
        answers[i] = answers[i] / temp;

        cudaMemcpy(matrixGPU, matrix, matrixBytes, cudaMemcpyHostToDevice);
        cudaMemcpy(answersGPU, answers, answerBytes, cudaMemcpyHostToDevice);

        jordanGPU << <1, 350 >> >(matrixGPU, answersGPU, i);

        cudaMemcpy(matrix, matrixGPU, matrixBytes, cudaMemcpyDeviceToHost);
        cudaMemcpy(answers, answersGPU, answerBytes, cudaMemcpyDeviceToHost);

        cudaFree(matrixGPU);
        cudaFree(answersGPU); 
```

Enter fullscreen mode Exit fullscreen mode

希望看到你们对我的代码的推荐和建议！
感谢阅读！~