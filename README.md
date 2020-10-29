clc;
clear all;
close all;
%distance of water coming out of orifice at height 2m can variate between
%0m to 5m
x=linspace(0,5);
%height of container is 5m with full of water
%heigth of orifice from bottom of container is 2m
y=2;
%heigth of water can vary from 5m to 2m
H=linspace(2,5);
x=sqrt(4*y*H-4*y^2); % Equation of path of water
figure;
plot(H,x); %plot for path
xlabel('Height of water from bottom(H)');
ylabel('Distance of water through orifice(x)');
title('Path of water coming from orifice');
%SAMPLING
figure;
stem(H,x); %plot of sampled signal
xlabel('Height of water from bottom(H)');
ylabel('Distance of water through orifice(x)');
title('Sampled Signal');
%QUANTIZATION
% n1=input('Enter the number of bits per sample : ');
n1=4;
L=2^n1;
xmax=2;
xmin=-2;
del=(xmax-xmin)/L;
partition=xmin:del:xmax; % definition of decision lines
codebook=xmin-(del/2):del:xmax+(del/2); % definition of representation levels
[index,quants]=quantiz(x,partition,codebook)
% gives rounded off values of the samples
figure(3);
hold on;
stairs(quants) %generates stair case type plot
title('QUANTIZED SIGNAL');
xlabel('Height of water from bottom(H) samples');
ylabel('Distance of water through orifice(x0)');
hold off;
%ENCODING
l1=length(index);
for i=1:l1
if(index(i)~=0)
index(i)=index(i)-1;
end
end

%NORMALISATION
l2=length(quants);
for i=1:l2
    if (quants(i)==xmax-del/2)
        quants(i)=xmax+del/2;
    end
    if (quants(i)==xmax+del/2)
        quants(i)=xmax-del/2;
    end
end

code=de2bi(index,'left-msb');

k=1;
for i=1:l1
    for j=1:n1
        coded(k)=code(i,j);
        j=j+1;
        k=k+1;
    end
    i=i+1;
end

%Ploting the Encoded Signal
figure(4);
stairs(coded);
title('encoded signal');
xlabel('Height of water from bottom(H)samples');
ylabel('Distance of water through orifice(x)');

%DECODING
index1=bi2de(code,'left-msb');
de_signal=(del*index1)+xmin+(del/2);

%Ploting the Decoded Signal
figure(5);
plot(de_signal);
title('Demodulated signal(Recontructed)');
xlabel('Height of water from bottom(H)');
ylabel('Distance of water through orifice(x)');
