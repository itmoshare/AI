comb(0,_,[]).
comb(N,[X|T],[X|Comb]):-N>0,N1 is N-1,comb(N1,T,Comb).
comb(N,[_|T],Comb):-N>0,comb(N,T,Comb).

delete(X,[X|T],T).
delete(X,[H|T],[H|NT]):-delete(X,T,NT).

varia_rep(0,_,[]).
varia_rep(N,L,[H|RVaria]):-N>0,N1 is N-1,delete(H,L,_),varia_rep(N1,L,RVaria).
    
zeroMove(Dx,Dy):-
    Dx =:= Dy, Dy =:= 0. 
checkResultMove(Xr, Yr):-
	Xr >= 0, Xr < 9, Yr >=0, Yr < 9.
moveFly([X, Y], [Xr, Yr]):-
    varia_rep(2,[-1,0,1],[Dx | [Dy | _]]),
    not(zeroMove(Dx, Dy)),
    Xr is X + Dx, Yr is Y + Dy,
    checkResultMove(Xr, Yr).

replace(_, _, [], []).
replace(O, R, [O|T], [R|T2]) :- replace(O, R, T, T2).
replace(O, R, [H|T], [H|T2]) :- H \= O, replace(O, R, T, T2).

replaceFlies([],[], Res, Res).
replaceFlies([FlyToReplace | ReplaceToTail],
             [FlyReplaceBy | ReplaceByTail],
             AllFlies, Res):-
    replace(FlyToReplace, FlyReplaceBy, AllFlies, NewFlies),
    replaceFlies(ReplaceToTail, ReplaceByTail, NewFlies, Res).

check(_, _, []).
check(X, Y, [ [Xt, Yt] | T]):-
    X \= Xt, Y \= Yt,
    Dt is Xt + Yt, D is X + Y, Dt \= D,%check diag
    check(X, Y, T).
checkField([]).
checkField([[X, Y] | TField]):-
    check(X, Y, TField),
    checkField(TField).
    
moveFlies(L):-
    comb(3, L, FliesToMove),
    nth0(0, FliesToMove, Fly1), 
    nth0(1, FliesToMove, Fly2), 
    nth0(2, FliesToMove, Fly3),
    moveFly(Fly1, NewFly1), 
    moveFly(Fly2, NewFly2), 
    moveFly(Fly3, NewFly3),
   	replaceFlies([Fly1,Fly2,Fly3], [NewFly1, NewFly2, NewFly3], L, Res),
    checkField(Res),
   	write(Fly1), write('--->'), write(NewFly1),nl(),
    write(Fly2), write('--->'), write(NewFly2),nl(),
   	write(Fly3), write('--->'), write(NewFly3),nl().     